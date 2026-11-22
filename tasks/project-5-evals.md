# Project 5: Eval and Observability Pipeline — Task Plan

**How to use this file**
- Do one task at a time, top to bottom. Each task should take 1–3 hours unless marked otherwise.
- A task is Done only when its "Done when" check passes. Record the evidence (numbers, file paths, trace or CI links) in the task's Result line.
- To update this file, tell Claude, for example: "Task 10 done, held-out kappa 0.71". Task state lives only here: tick the TOC box and fill the Result line. Items to raise at a later task go in that task's `Open:` line and are deleted when resolved. `progress.md` changes only when a checkpoint or the project Status changes.

**Stack**
- TypeScript for tracing, the eval runner and the CI gate. Python only for the agreement statistics in task 10 (a short script; each non-obvious construct gets a one-line comment).
- Tracing and datasets: Langfuse (already used in P2 task 6). Check its current dataset and experiment features before writing your own storage for them.
- CI: GitHub Actions. P1's repo needs to be pushed to GitHub for this (it is local-only today).
- No eval framework at first. Ragas is reported inactive since 2026-02 and DeepEval is Python (unverified, see `references/guides-extract.md`, section 9); in TypeScript, custom scorers are a few lines each.

**Where the code lives (suggestion):** tracing and the CI gate go into the P1 repo, because that's where the PRs are. The judge, the labels and the stats script go into `projects/evals-pipeline`, a repo of their own. Decide in task 1.

**Where the checkpoints land:** 1 → task 2 (with P2 task 6) · 3 → task 3 · 2 → task 5 · 4 → task 7 · 5 (judge calibration) → task 11. Background for judge calibration: `references/guides-extract.md`, section 5.3.

**Prerequisites:** P1 at least through task 12 (answers with citations), P2 through task 6 (a traced run).

---

## TOC

**Phase A: Traces**
1. [ ] Decide the layout and set up Langfuse
2. [ ] Trace P1 end to end

**Phase B: Eval runs and the CI gate**
3. [ ] One-command eval runner: accuracy, latency, cost
4. [ ] Measure run-to-run variance, set the threshold
5. [ ] CI gate, proven on a deliberately bad PR

**Phase C: Error analysis feeds the dataset**
6. [ ] Read and label failures from traces
7. [ ] Fix one failure, add failures to the eval set

**Phase D: Judge calibration**
8. [ ] Rubric and judge
9. [ ] Hand-label ~100 outputs (2+ sittings)
10. [ ] Agreement: kappa, Spearman, rubric iterations
11. [ ] Bias numbers: position flip, length, self-preference

**Phase E: Wrap-up**
12. [ ] Write-up README

---

## Phase A: Traces

### 1. Decide the layout and set up Langfuse
**Description**
Decide where each piece lives (see "Where the code lives"). Create `projects/evals-pipeline` if you keep the judge separate. Confirm the Langfuse project from P2 task 6 works, or set it up now.
**Deliverables**
- A short "Layout" section in the evals repo README
**Done when:** you can open the Langfuse project and see the P2 trace.
**Time:** ~1h
**Result:**

### 2. Trace P1 end to end
**Description**
Add tracing to P1's `ask.ts`: one trace per question, spans for embed, each retriever, fusion, rerank and generation, each with latency; tokens and cost on the generation span. Tag traces with the git commit so runs can be compared.
**Deliverables**
- Tracing in the P1 repo
- A P1 trace linked or screenshotted next to the P2 one
**Done when:** a P1 trace shows every pipeline stage with timings. **Checkpoint 1 done** (with P2 task 6).
**Time:** ~2h
**Result:**

## Phase B: Eval runs and the CI gate

### 3. One-command eval runner: accuracy, latency, cost
**Description**
Write `npm run eval` in P1. It runs the full eval set and writes one JSON file per run: retrieval metrics (Pass@k, MRR), answer metrics (valid citations), latency p50/p95, total cost, and the pass/fail result of every case. Keep these runs in the repo or in Langfuse.
**Deliverables**
- `eval/run.ts` and `eval/runs/<date>-<commit>.json`
- A table of the last 3 runs in `results.md`
**Done when:** one command produces a run file with all three kinds of number. **Checkpoint 3 done.**
**Time:** ~2–3h
**Result:**

### 4. Measure run-to-run variance, set the threshold
**Description**
Run the eval 3 times on the same commit. Retrieval should be identical (deterministic); answers may not be. Count how many cases flip between identical runs. That is your noise floor. Set the gate threshold in cases, above that noise. Percent thresholds from guides (for example "warn at 3%") are 1–3 cases on a 40–100 question set, which is noise.
**Deliverables**
- A "Variance" note in `results.md`: flips per metric across the 3 runs and the chosen threshold
**Done when:** the threshold is written down with the noise it is based on.
**Time:** ~1h
**Result:**

### 5. CI gate, proven on a deliberately bad PR
**Description**
GitHub Actions workflow on every PR: run the eval, compare with the stored baseline run from `main`, fail if a headline metric drops by more than the threshold. Print flip lists: cases that went pass→fail and fail→pass. Put the API key in repository secrets and cap spend (cheap model for CI, or retrieval-only on every PR and full evals on demand). Then open a PR that makes things worse on purpose (for example drop the reranker or weaken the prompt) and watch it fail.
**Deliverables**
- `.github/workflows/eval.yml`
- Link to the failing PR run, with its flip list
**Done when:** the bad PR is blocked and its output lists the flipped cases. **Checkpoint 2 done.**
**Time:** ~3h
**Result:**

## Phase C: Error analysis feeds the dataset

### 6. Read and label failures from traces
**Description**
Open 30+ traces from P1 and P2, including every failed eval case. Write a one-line note per failure, then group the notes into categories. Start from P1's (bad chunking, vocabulary mismatch, bad gold label, vague question) and this taxonomy for pipelines and agents: extraction hallucination, misclassification, propagation error, prompt failure, context loss. Count each.
**Learn:** Hamel Husain on error analysis (resource map, section 7).
**Deliverables**
- `error-analysis.md`: categories with counts and 1–2 trace links each
**Done when:** the top failure category is named with its count.
**Time:** ~2–3h
**Result:**

### 7. Fix one failure, add failures to the eval set
**Description**
Fix the top category and re-run the eval. Then add the failing cases to the eval set with expected outputs you have reviewed by hand. Tag every case with `origin`: `hand`, `generated` or `trace`. If you let a model draft expected outputs, send low-confidence ones to your review, and check new cases for near-duplicates of existing ones before adding.
**Deliverables**
- Before/after numbers in `results.md`
- Updated eval set with origin tags; counts per origin
**Done when:** one fix is measured (even if the effect is zero) and trace-origin cases are in the set. **Checkpoint 4 done.**
**Time:** ~2h
**Result:**

## Phase D: Judge calibration

### 8. Rubric and judge
**Description**
Pick P1 answers as the judged output. Write a rubric with 3–5 criteria (for example faithful to sources, answers the question, citations correct), each on a 3- or 5-point scale with every point defined in words; avoid 1–10 scales. The judge returns reasoning first, then scores, as JSON validated with Zod. Use a different model from the one that generated the answers.
**Deliverables**
- `judge/rubric.md`, `judge/judge.ts`
**Done when:** the judge scores 10 answers and every output validates.
**Time:** ~2h
**Result:**

### 9. Hand-label ~100 outputs (2+ sittings)
**Description**
Collect about 100 P1 answers: easy, hard, adversarial, and some deliberately broken (wrong citation, unsupported claim, off-topic). Label them yourself against the rubric, without looking at the judge's scores. On a different day, re-label 20 of them blind: your agreement with yourself is the ceiling for the judge. Set aside about 30 as held-out; never tune on them. If time is tight, 50 labels is the floor.
**Deliverables**
- `judge/labels.jsonl` with a `split` field (`tune` or `heldout`) and the 20 re-labels
**Done when:** ~100 labels exist, plus your self-agreement on the 20.
**Time:** ~4–5h over 2+ sittings (exception to the 1–3h rule)
**Result:**

### 10. Agreement: kappa, Spearman, rubric iterations
**Description**
Write `judge/agreement.py`: weighted Cohen's kappa (quadratic weights; `sklearn.metrics.cohen_kappa_score`) and Spearman correlation (`scipy.stats.spearmanr`), judge against your labels, per criterion. Run it with `uv run`, dependencies declared inline. Read the 20 largest disagreements: usually the rubric wording is ambiguous, not the model wrong. Sharpen the wording, add 2–3 anchor examples per score point, re-run on the tune split. Do 2–3 iterations, then report once on held-out.
**Deliverables**
- `judge/agreement.py`, a chart or table of agreement per iteration, final held-out numbers
**Done when:** held-out kappa and Spearman are recorded next to your self-agreement.
**Time:** ~3h
**Result:**

### 11. Bias numbers: position flip, length, self-preference
**Description**
Three checks. **Position flip:** judge 30+ pairs of answers in both orders; count how often the winner changes (above ~10% is a warning sign, a rule of thumb). **Length bias:** compare scores against answer length for answers you labelled equal. **Self-preference:** score the same pairs with a judge from another model family (the P2 gateway's second provider) and compare.
**Deliverables**
- The three numbers in the README, each with one sentence on what it means for trusting the judge
**Done when:** all three are recorded. **Checkpoint 5 done.**
**Time:** ~2–3h
**Result:**

## Phase E: Wrap-up

### 12. Write-up README
**Description**
Lead with results: the CI gate catching the bad PR, the fix found through error analysis with before/after numbers, judge-to-human agreement against your self-agreement, the three bias numbers. Then what each piece costs per run and what you'd automate next (for example the P1 citation-support check with the calibrated judge).
**Deliverables**
- `README.md` in the evals repo
- Repo link in `progress.md`
**Done when:** a stranger can re-run the agreement script and the eval runner by following the README.
**Time:** ~2h
**Result:**
