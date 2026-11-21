# Progress Tracker: Becoming a 10x AI-Native Engineer

Resources for each project are in `ai-native-engineer-resources.md`. Step-by-step task plans live in `tasks/` (one file per project). This file tracks what is actually done. To update it, tell Claude (for example "record progress: RAG baseline works, 40 eval questions").

**Rules for keeping it honest**
- A checkpoint counts only when its evidence exists (demo link, eval result, benchmark number, trace).
- Record numbers, not feelings. Record what failed too.
- Update on milestones, not on every message. One milestone updates three places together: the task's `Result` line, the checkpoint and `Status` here, and the log.

**Status key:** Not started / In progress / Done

**Planned order (suggestion, can change):** 1, 2, 5, then optional 6 and 7, then 4, 3. Start the eval set during project 1. Projects 6 and 7 come from the second source post (resource map, section 11).

---

## Project 1: Production RAG (hybrid search, reranking, citations)
Status: In progress
Task plan: `tasks/project-1-rag.md` (16 tasks, 2 optional)

Checkpoints (evidence in brackets):
- [ ] Baseline RAG answers questions over a chosen document set [demo or repo link]
- [ ] Eval set of questions with expected sources [count + file location]
- [ ] Hybrid search (vector + BM25) added [retrieval hit-rate and MRR vs baseline, per question category]
- [ ] Reranking added [hit-rate vs hybrid]
- [ ] Answers carry citations [example output + % of 20 hand-checked answers where the cited chunk supports the claim]
- [ ] Short write-up with before/after numbers [README link]

Repo / links: `projects/rag-hybrid-search` (own git repo, local only, not pushed). `main` = TypeScript; branch `python-version` keeps the earlier Python work.
Notes: Tasks 1–2 done. Corpus (2026-11-22): 91 Angular docs (forms guides + error reference) at `angular/angular@8b15295`. Next: task 3, load and chunk the documents.

## Project 2: Agent + MCP server
Status: Not started
Task plan: `tasks/project-2-agent-mcp.md` (14 tasks)

Checkpoints:
- [ ] MCP server exposing at least two tools [repo link; MCP spec version targeted]
- [ ] Agent completes a real multi-step task using those tools [demo or trace]
- [ ] Failures and limits handled: tools return a structured status (ok, empty, rate-limited, timeout) instead of throwing; budget ceiling in code (tokens, dollars, steps, wall-clock) [test showing the run stopping at the ceiling and recovering from a tool failure]
- [ ] (From guides) Gateway-lite: one `callModel()` module, two providers, required metadata, schema validation with one repair then fallback, retries by error type, circuit breaker, chaos switch, cost per call [trace of a forced failure showing fallback and breaker states; test for repair and fallback]
- [ ] (From guides) Attack your own agent: 20–30 attack rows in 6 families, layered defences including a required approval step before irreversible actions [attack success rate before/after by family; false-positive rate on ~30 benign inputs; one bypass found and closed]
- [ ] Short write-up of design choices [README link]

Repo / links: (none yet)
Notes: (none yet)

## Project 5: Eval and observability pipeline
Status: Not started
Task plan: `tasks/project-5-evals.md` (12 tasks)

Checkpoints:
- [ ] Traces captured for projects 1 and 2 [tracing tool + screenshot or link]
- [ ] CI gate: evals run in CI and fail the PR when a headline metric drops beyond a threshold set in cases; report lists pass→fail and fail→pass flips [run output from a deliberately bad PR]
- [ ] Accuracy, latency and cost tracked per run [table or dashboard]
- [ ] Error analysis feeds the dataset: at least one failure found and fixed; failures added to the eval set with hand-reviewed expected outputs, each case tagged by origin (hand, generated, trace) [before/after + case count by origin]
- [ ] (From guides) Judge calibration: rubric judge checked against ~100 hand labels [kappa + Spearman on held-out labels; position-flip, length-bias and self-preference numbers]

Repo / links: (none yet)
Notes: (none yet)

## Project 6: Semantic caching layer (optional, from second post)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Cache in front of the model (through the P2 gateway if it exists): exact match first, then semantic match, otherwise call and store [repo link]
- [ ] Labelled set of paraphrase pairs and near-miss pairs [count + file location]
- [ ] Similarity threshold chosen from a curve, plus a hand- or judge-checked sample of hits [hit rate and false-hit rate at 4+ thresholds]
- [ ] Hit rate, latency (hit vs miss) and cost saved reported; provider prompt caching reported separately [numbers]
- [ ] Cache key and invalidation: key includes model, temperature, tool set, tenant and system-prompt version; TTL [tests showing no cross-tenant hit and that a system-prompt change busts the cache]

Repo / links: (none yet)
Notes: (none yet)

## Project 7: Text-to-SQL with guardrails (optional, from second post)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Question to SQL over a sample Postgres database [repo link]
- [ ] Generated SQL parsed and destructive statements blocked [test output]
- [ ] Execution accuracy (execution match, not SQL text match) measured on a 30 to 50 question set, including ~5 ambiguous questions (expected: a clarifying question) and ~5 unanswerable ones [score]
- [ ] Adversarial prompts tried (writes, multi-statement, hidden columns), using P2's attack-row format [attack success by family, false-block rate on benign questions]
- [ ] Writes fail at the database level (read-only role) even if the parser is bypassed [test output]
- [ ] Results returned with the SQL shown [example output]

Repo / links: (none yet)
Notes: (none yet)

## Project 4: Self-hosted LLM with vLLM
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] vLLM serving a model through the OpenAI-compatible endpoint [command + hardware used]
- [ ] Benchmark run: throughput, TTFT, TPOT recorded, with concurrency raised in steps [numbers + the concurrency where p95 latency starts to degrade]
- [ ] One of projects 1 or 2 pointed at the self-hosted endpoint, as a P2 gateway provider with fallback to the hosted API [result vs hosted API]
- [ ] Write-up with the numbers and hardware, including cost per 1,000 requests with idle GPU time and a break-even volume against the hosted API [README link]

Repo / links: (none yet)
Notes: (none yet)

## Project 3: LoRA/QLoRA fine-tune (optional, last)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Task with a hard correctness signal (schema validates or not, or an exact label) and a held-out test set of ~100–200 examples never used for training or tuning [task + set size]
- [ ] Prompting/RAG baseline measured on the held-out set first [score]
- [ ] LoRA or QLoRA run completes on a small model, plus one ablation (for example rank 8 vs 32) [rank, alpha, learning rate, loss]
- [ ] Fine-tuned model compared with the baseline on the same held-out set; headline numbers are eval metrics, not training loss [scores]
- [ ] Decision recorded with numbers: schema validity and field accuracy vs baseline, cost per 1,000 requests, p95 latency, break-even volume. "The prompt wins" is a valid outcome [one paragraph]

Notes before starting: don't train only on synthetic outputs of the model you compare against; check provider terms before training on a frontier API's outputs (unverified). If P4 is done, serve the adapter through vLLM (`--enable-lora`).

Repo / links: (none yet)
Notes: (none yet)

---

## Side tracks (not projects)

**Weekend certifications (postponable)**
Task plan: `tasks/weekend-certs.md` (8 core + 5 optional, phased to match projects 1 → 2 → 5 → 4/3; 2–3h per weekend day)
- [ ] Phase 1, Project 1 (Claude Code courses, Building with the Claude API, MongoDB RAG badges) [cert links]
- [ ] Phase 2, Project 2 (Intro to MCP, HF MCP Unit 1, HF Agents Unit 1) [cert links]
- [ ] Phase 3, Project 5 (HF Agents observability and evaluation unit) [trace link]
- [ ] Phase 4, Projects 4 and 3 (Kaggle Python, Kaggle Intro to Deep Learning) [cert links]

**Working with AI coding agents**
- [ ] Claude Code best practices read and applied to one real repo
- [ ] Notes: (none yet)

**Backend and system-design gaps**
- [ ] Gaps to cover: (add as chosen)
- [ ] Notes: (none yet)

---

## Log (newest first)

- 2026-11-22: Project 1, task 2 done. Corpus: 91 Angular docs Markdown files (`guide/forms` 22 incl. 16 Signal Forms, `reference/errors` 69) from `angular/angular@8b15295` (2026-11-20), MIT license and attribution in the README. First copy nested the error pages in `errors/errors/`, fixed. 8 seed questions (4 error codes, 4 forms) written with their source files; see the task 2 Result. Open question for task 4: classic and Signal Forms guides can both answer some questions.
- 2026-11-19: Integrated the third source, three PDF project guides (extract in `references/guides-extract.md`; PDFs kept in Google Drive). Checkpoints changed: P1 (MRR and per-category hit rate; citation support on 20 hand-checked answers), P2 (now 6: failures and limits, gateway-lite, attack your own agent; the optional approval step is now part of the defence), P5 (now 5: CI gate, error analysis feeds the dataset, judge calibration), P6 (threshold curve at 4+ thresholds, cache key and invalidation), P7 (ambiguous and unanswerable questions, attack-row format), P4 (concurrency steps, break-even with idle GPU cost), P3 (held-out set, ablation, decision with numbers). Task plans added: `tasks/project-2-agent-mcp.md` (14 tasks), `tasks/project-5-evals.md` (12 tasks); P1 tasks 4, 7 and 12 updated. Weekend certification track moved into `tasks/`. Images renamed. No project work done.
- 2026-11-18: Project 1, task 1 done. Local embedding (`all-MiniLM-L6-v2-ONNX`, 384 dims, normalized, ~10–15 ms per embed) and local rerank spike passed (`Xenova/bge-reranker-base`, q8, 266 MB): relevant 7.18 vs non-answers 0.25 and 0.39, 3 pairs in ~124–141 ms, cached load ~700 ms. No hosted fallback needed. Findings: the transformers.js `text-classification` pipeline can't score pairs (use tokenizer `text_pair` + `AutoModelForSequenceClassification`), and Node defaults to fp32 unless `dtype: "q8"` is set. A run with a truly unrelated third chunk (CSS Grid) scored 6.88 / -0.26 / -10.18, so off-topic text lands far below near-topic non-answers.
- 2026-11-17: Project 1 repo re-initialised as TypeScript. Old Python work (3 commits) kept on branch `python-version`; `main` reset to the initial commit plus `46e7e2e` "Initialize TypeScript project with dependencies" (npm, tsconfig, `@anthropic-ai/sdk`, `@huggingface/transformers`, `dotenv`, `tsx`, `vitest`). `scripts/smoke-test.ts` ported from the Python version: typecheck clean, Claude reply printed, stop_reason `end_turn`. Not yet done: embedding and rerank spike. Watch item: npm skipped install scripts for `protobufjs` and `fsevents`; check this first if the ONNX runtime fails to load.
- 2026-11-17: Decision: Project 1 switches from Python to TypeScript, so the RAG concepts aren't learned alongside a new language (also matches the TypeScript-end-to-end target posting). Python stays for projects 3 and 4. Risk: local embeddings/reranker move from `sentence-transformers` to transformers.js (ONNX); task 1 now includes a spike, with a hosted-API fallback. Updated `tasks/project-1-rag.md` (stack, tasks 1, 3, 6, 7, 11 and file names). The Python scaffold in `projects/rag-hybrid-search` still needs replacing.
- 2026-11-17: Synced local files with the Claude Project export. Added the second source post to the resource map (sections 11 to 13), optional projects 6 (semantic caching) and 7 (text-to-SQL guardrails) and extra checkpoints for projects 2 and 5 to this tracker, and a new doc, `job-postings.md`. No local content was overwritten.
- 2026-11-16: Project 1, task 1 in progress. uv project and deps committed; smoke test calls Claude successfully (stop_reason `end_turn`). Embedding check still missing. First run hit a 400 "credit balance too low" error before it passed.
- 2026-11-16: Created the local repo `rag-hybrid-search` for Project 1 (inside `projects/`, not pushed to GitHub). No task done yet.
- 2026-11-16: Workspace cleanup. Moved the Project 1 plan to `tasks/project-1-rag.md`, fixed paths in all files, rewrote `AGENTS.md`. No project work started yet.
- 2026-11-15: Task plan for Project 1 added. No project work started yet.
- 2026-11-15: Project set up. Custom instructions written, resource map added (`ai-native-engineer-resources.md`), this tracker created. No project work started yet.
