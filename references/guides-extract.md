# Project Guides: Extract and Integration Plan

Created 2026-11-18 from three PDF project guides for AI engineers (kept in Google Drive, not in this repo). Moved here and applied 2026-11-19.

**Status: applied 2026-11-19.** Sections 5 and 6 are now in `progress.md` (checkpoints), `tasks/project-1-rag.md` (tasks 4, 7, 12), `tasks/project-2-agent-mcp.md` and `tasks/project-5-evals.md`. This file stays as the reasoning and the full reference specs behind those changes.

**Decisions made in the 2026-11-18 discussion**
- All seven projects stay, as separate repos. The optional ones (P3, P4, P6, P7) are not started immediately.
- TypeScript is the main language; Python only where required. Heavy LLM assistance, stated openly.
- Add three things from the guides: judge calibration, a gateway, and attacking your own agent. No new projects or repos; they become checkpoints inside existing projects.
- Borrow extra checkpoints for the existing projects (section 6).

**Contents:** 1 The three guides · 2 What to make of them · 3 Sorting the 27 projects · 4 Names used in this file · 5 The three additions · 6 Borrowed checkpoints per project · 7 Skipped projects · 8 Context only: job posting and capstone · 9 Verification notes · 10 Open questions · 11 Sources

---

## 1. The three guides

| Short name | Pages | Style |
|---|---|---|
| **short guide** | 9 | Short spec per project: use case, build, stack, ship gate, "trap" |
| **six guide** | 21 | Five phases per project, "Done when", resume line, common mistakes, stretch goals |
| **15 guide** | 50 | Six phases per project, day-by-day (about 12-14 days at 2-3 h/day each) |

**Projects in the short guide:** Fieldnote (multimodal RAG), Casefile (multi-agent claims triage), Warmstart (LLM caching), Groundtruth (retrieval evaluation), Slotfill (LoRA fine-tuning for invoice extraction), Doorman (prompt-injection guardrails).

**Projects in the six guide:** Knowledge Graph RAG, Multi-Agent Research Assistant, Self-Healing LLM Gateway, LLM-as-Judge with Human Calibration, Model Distillation Pipeline, Automated Red-Team Harness.

**Projects in the 15 guide:** 1 Model Regression Detection, 2 LLM Cost Autopilot, 3 Failure Forensics, 4 Self-Healing Docs, 5 Output Arbitration, 6 Hybrid-Search RAG, 7 Semantic Caching, 8 Text-to-SQL with Guardrails, 9 Prompt Versioning and A/B, 10 LoRA Fine-Tuning, 11 LLM Gateway, 12 AI Feature Flags, 13 Eval Dataset Generator, 14 Multimodal Document Processor, 15 Agent Orchestration with Human-in-the-Loop.

## 2. What to make of them

- **Source:** all three promote one paid online community. Pricing not checked. No citations, no code; every number is the authors' illustration.
- **Link to the second social post** (resource map, section 11): its six cards have the same titles as 15 guide projects 6, 15, 7, 13, 10 and 8. The stacks on the cards differ from the guide's. Same author is not confirmed.
- **Shared advice worth keeping:** a project counts only with an eval, a number and a tradeoff you can defend. Build the eval before the feature. README opens with the result. Write down what did not work. Build one to three projects well (short guide: "pick one"; 15 guide: "two or three").
- **Quality:** short guide is best for acceptance gates. Six guide is best for build phases. The 15 guide is an idea menu: centred on GPT-4o, several rebuilds of existing tools, a few flaws (section 7).

## 3. Sorting the 27 projects

One question: does it give you something your seven projects do not?

**Add (5 entries, 3 projects):** new to you.
- Self-Healing LLM Gateway (six guide), with LLM Gateway with Rate Limiting and Budgets (15 guide) as a stretch
- LLM-as-Judge with Human Calibration (six guide)
- Doorman (short guide) plus Automated Red-Team Harness (six guide), merged

**Borrow from (13 entries):** another version of a project you already have; only the extra checkpoints are taken.
- P1 RAG: Hybrid-Search RAG (15)
- P2 Agent + MCP: Casefile (short), Multi-Agent Research Assistant (six), Agent Orchestration (15)
- P5 Evals: Groundtruth (short), Model Regression Detection (15), Eval Dataset Generator (15)
- P6 Cache: Warmstart (short), Semantic Caching (15)
- P7 Text-to-SQL: Text-to-SQL with Guardrails (15)
- P3/P4: Slotfill (short), Model Distillation (six), LoRA Fine-Tuning (15)

**Skip (9 entries):** see section 7.

## 4. Names used in this file

P1 RAG · P2 Agent + MCP · P3 LoRA/QLoRA · P4 vLLM · P5 Evals and observability · P6 Semantic cache · P7 Text-to-SQL. "Effort" numbers in sections 5 and 6 are my estimates, not from the guides.

---

## 5. The three additions

**Net effect.** Still 7 projects, no new repos. P2 grows by two checkpoints. P5 ends with one fewer checkpoint than before (two merges, one addition). Other projects get swaps only. Estimated extra effort: gateway 2-4 days, attack test 1-2 days, judge calibration about one weekend, small borrowed items minutes to a couple of hours each. Total roughly 1-2 weeks, mostly in P2. If it feels too much: cut judge labels from 100 to 50 and attack cases from 30 to 20 before dropping anything, and keep the gateway.

### 5.1 Gateway-lite (goes in P2, TypeScript)

**What we do (scaled down)**
- One module, `callModel(request)`, is the only way the agent talks to a model.
- Two providers behind one interface. (The target posting names OpenRouter and Cloudflare AI Gateway as hosted examples; writing the thin module yourself teaches the mechanics. A hosted gateway can be compared later.)
- Metadata required on every call: request id, feature, tenant. Without it you cannot attribute cost.
- Output validated against a schema (Zod). On invalid output: repair once, then fall back to the second provider.
- Error taxonomy: rate limit, timeout, server error, content filter, auth failure. Retry only retryable errors; fall back after retries are exhausted.
- Circuit breaker per provider with closed, open and half-open states. Half-open sends a small share of traffic to a recovering provider.
- Cost per call (tokens x price) logged into the Langfuse trace.
- Chaos switch (env flag or wrapper) that injects errors or latency into one provider.

**Done when:** a forced failure shows the fallback and the breaker states in a trace; a test covers schema repair and fallback; cost per call is visible.

**Reuse:** P5 traces its calls; P6's cache sits in front of it; P4's vLLM endpoint and P7's calls can be providers.

**Left out on purpose:** Prometheus/Grafana, hedged requests (they roughly double spend on hedged calls), Redis queue for deferrable work, per-team rate limits and budgets, hot-reload config, the 5,000-request load test.

**The guides' mistake list:** no cost attribution; retrying calls that are not idempotent; no half-open state; a dashboard with no chaos test.

**Caution:** LiteLLM (Python) had two malicious releases, 1.82.7 and 1.82.8, on 2026-03-24 (Secondary sources); a clean 1.83.0 followed. Not needed for a TypeScript module. If a Python project uses it, pin and hash-verify.

**Full guide spec (reference only)**
- Six guide, Self-Healing LLM Gateway, 2-3 weeks part-time:
  1. Route every call through one OpenAI-compatible service, 3+ providers, metadata required.
  2. Health per provider: rolling success rate, p50/p95/p99, error taxonomy, Redis sliding window, `/metrics`.
  3. Failover: breaker per provider, preference lists per request class, half-open probes, hedged requests for latency-sensitive classes.
  4. Queue and retry deferrable work with backoff, jitter and idempotency keys.
  5. Grafana board plus a chaos endpoint; record 90 seconds of healthy, degraded, tripped, rerouted, recovered.
- 15 guide, LLM Gateway, adds: team API keys, Redis token-bucket rate limits with `Retry-After`, budget caps with an 80% warning, priority tiers, OpenTelemetry, a 5,000-request load test, and a "<10 ms overhead" target.

### 5.2 Attack your own agent (goes in P2)

**What we do (scaled down)**
- Target: the P2 agent with its real tools. The tools are the attack surface.
- 20-30 attacks stored as rows: id, family, payload, expected safe behaviour. Families: direct instruction override; injection hidden in a document or tool result; call to a tool outside the allowed set; data exfiltration through output (for example a URL carrying data); encoding tricks (base64, unusual unicode); system-prompt extraction.
- Layered defence: untrusted content isolated and labelled; per-context tool allowlist; confirmation (the approval step, now required rather than optional) before irreversible actions; output scan for secrets and URLs; every block logs the rule that fired.
- Report: attack success rate before and after the defences, by family; false-positive rate on about 30 benign inputs; one bypass you found and closed, written up; every block traceable to a rule.
- Principle from the guides: filtering the phrase "ignore previous instructions" is theatre. The real control is limiting what the agent can do once it is fooled. A system prompt that asks the model nicely is not a security control.
- Tools: garak (NVIDIA) for generic probes; PyRIT (Microsoft) for multi-turn attacks. garak scans the model and does not know your prompts or tools, so the app-specific attacks are yours to write. Use only against your own systems.

**Reuse:** P7's adversarial-prompt checkpoint uses the same attack-row format and reporting.

**Left out on purpose:** automatic GitHub issue filing, scheduled runs, dashboard, trained classifier.

**Full guide spec (reference only)**
- Short guide, Doorman (1-2 weekends): a recruiting agent that reads candidate uploads and holds email, scoring and ATS-write tools. Layered defence: input classification, hard isolation of untrusted content, per-context tool allowlist, output scanning, confirmation on irreversible actions. Red-team suite of 60 attacks (including injection in resume metadata and in a tool result), 100 benign resumes for false positives, one documented bypass closed, every block traceable to a rule.
- Six guide, Automated Red-Team Harness (3-4 weeks): attack battery from garak and PyRIT plus your own domain cases; a target-adapter interface; deterministic checks first (prompt leak, PII pattern, blocked tool called, data in outbound URL), then a judge; three outcomes (pass, fail, needs review); findings auto-filed as GitHub issues with severity and repro, de-duplicated by attack id plus response signature; every confirmed finding becomes a permanent regression case; dashboard of findings over time. Scope note: run it against applications you own only. Stretch: adaptive and multi-turn attacks, coverage against the OWASP LLM Top 10.

### 5.3 Judge calibration (goes in P5)

**What we do (scaled down)**
- Pick one task with real outputs (P1 answers are the natural choice).
- Rubric: 3-5 criteria, each on a 3- or 5-point scale with every point defined in words. The six guide warns against 1-10 scales. The judge returns its reasoning first and scores second; measure whether that helps instead of assuming.
- Hand-label about 100 outputs (the six guide uses 200), mixing easy, hard, adversarial and deliberately broken ones. The labels must be yours, not an LLM's. Label 20 of them twice on different days; that self-agreement is the ceiling for the judge.
- Compute weighted Cohen's kappa and Spearman correlation, judge against your labels. Read the 20 largest disagreements (the six guide: usually the rubric is ambiguous, not the model wrong). Sharpen the wording, add 2-3 anchor examples per score point, re-run, and chart agreement per iteration. Keep part of the labels held out so you do not tune and report on the same examples.
- Three bias numbers: position-flip rate (judge pairwise in both orders; the six guide treats more than about 10% as untrustworthy, a heuristic), length bias (score against output length at fixed quality), self-preference (swap the judge's model family on the same pairs).
- Do not judge outputs with the same model that produced them.
- Kappa and Spearman are easiest in a small Python script (a case where Python is required). DeepEval is a Python library; in a TypeScript repo use custom scorers.

**Done when:** README reports judge-to-human kappa and Spearman plus the three bias numbers. The CI gate is the one in P5 checkpoint 2 (section 6).

**Full guide spec (reference only):** six guide, LLM-as-Judge with Human Calibration, 2-3 weeks. Phases: rubric and structured judge; 200 hand labels with a Streamlit labelling UI and Postgres; agreement and gap closing; bias tests; gate releases in CI with DeepEval, stored runs and trend chart, demo with a PR that weakens a prompt and fails CI. Stretch: active learning, drift detection. Mistakes listed: no human baseline; 1-10 scales; judging with the model that produced the output; tuning the rubric on the examples you report agreement on.

---

## 6. Borrowed checkpoints per project

For each project: what the tracker had on 2026-11-18 and what changed. Applied 2026-11-19.

### P1 RAG (TypeScript, plan in `tasks/project-1-rag.md`)
- **Task 7 (vector search + Pass@k):** also record MRR, and Pass@k per question category (tag each eval question as lookup, paraphrase or exact-term; task 4 already mixes these). Use nDCG only if questions have several gold chunks. About +30 min. Source: Groundtruth.
- **Task 12 (citations):** besides "citation exists", hand-check 20 answers for whether the cited chunk supports the claim; report the percentage. About +1 h. Source: Hybrid-Search RAG (15). A calibrated judge from P5 can automate it later.
- **results.md:** keep retrieval columns (Pass@k, MRR) separate from answer-quality columns (citations, support). Source: Groundtruth. No extra effort.
- **Not borrowed:** three chunking strategies, semantic chunking, chunk de-duplication (task 14 stays optional), CI gate (P5 owns it).

### P2 Agent + MCP (TypeScript)
Checkpoints (6, was 4 plus 1 optional):
1. MCP server exposing at least two tools. *Unchanged.*
2. Agent completes a real multi-step task using those tools. *Unchanged.*
3. **Failures and limits handled** (replaces "Tool errors and timeouts handled"): tools return a structured status (ok, empty, rate-limited, timeout) instead of throwing; a budget ceiling enforced in code (tokens, dollars, steps, wall-clock); a test shows the run stopping at the ceiling and recovering from a tool failure. If the agent has a reviewer or retry loop, cap it at one send-back. Source: Casefile, Multi-Agent Research Assistant.
4. **New: gateway-lite** (5.1).
5. **New: attack your own agent** (5.2). The approval step, previously optional, becomes part of the defence.
6. Short write-up of design choices. *Unchanged.*

Not added: kill-and-resume from a stored snapshot (needs persistence); replayable step logs stay optional (Langfuse traces cover most of it); agent memory systems and parallel specialists from the 15 guide.
None of the 27 guide projects builds an MCP server, so checkpoint 1 stays as is.

### P3 LoRA/QLoRA (Python)
- Choose a task with a hard correctness signal (strict schema either validates or not, or an exact label). Source: Slotfill.
- Held-out test set (about 100-200 examples) never used for training or tuning. Headline numbers are eval metrics, never training loss. Source: Slotfill, Distillation, LoRA Fine-Tuning.
- Do not train only on synthetic examples from the model you are comparing against (Slotfill's "trap").
- "Decision recorded" checkpoint gets numbers: schema validity and field accuracy against the prompted baseline, cost per 1,000 requests, p95 latency, and a break-even request volume. "The prompt wins" is a valid outcome.
- Log rank, alpha and learning rate; run one ablation (for example rank 8 against 32). One extra training run.
- Not borrowed: 3+ configuration sweeps, catastrophic-forgetting benchmarks, A/B endpoint.
- Dataset sizes disagree across the guides (500-2,000; 1,000+ with 200 held out; 5,000-10,000). Treat as heuristics.
- **Unverified:** using a frontier API's outputs as training data may be restricted by provider terms. Check before doing it.

### P4 vLLM (Python tooling, needs a GPU)
- **Benchmark:** raise concurrency in steps and record where p95 latency starts to degrade. Source: Distillation.
- **Write-up:** cost per 1,000 requests including idle GPU time, and a break-even request volume against the hosted API. Leaving out idle time makes self-hosting look cheaper than it is (the guide lists this as a common mistake).
- **"Point P1 or P2 at the self-hosted endpoint":** use the P2 gateway with the vLLM endpoint as a provider and a fallback to the hosted API; compare results.
- **If P3 is done:** serve its adapter through vLLM's LoRA support (`--enable-lora`; documented in the vLLM docs, v0.14.0 page seen).
- Not borrowed: student-to-teacher escalation router (the gateway fallback covers it), quantisation stretch.

### P5 Evals and observability (TypeScript, stats script in Python)
Checkpoints (5, was 6):
1. Traces captured for P1 and P2. *Unchanged.*
2. **CI gate** (merges "automated evals from command or CI" and "new version scored, regressions flagged"): evals run in CI and fail the PR when a headline metric drops beyond a threshold; demonstrated on a deliberately bad change; the report lists cases that flipped pass to fail and fail to pass. Source: Groundtruth, Model Regression Detection. Note: the 15 guide suggests warn at 3% and critical at 8%, but with 40-100 cases that is 1-3 cases, which is noise. Use flip lists, set the threshold in cases, and tune it after seeing run-to-run variance.
3. Accuracy, latency and cost per run. *Unchanged.*
4. **Error analysis feeds the dataset** (merges "failure found and fixed" and "eval dataset from real traces"): at least one failure found and fixed; failures added to the eval set with expected outputs hand-reviewed, each case tagged by origin (hand, generated, trace). Source: Eval Dataset Generator (send low-confidence auto-labels to human review; check for near-duplicates before adding).
5. **New: judge calibration** (5.3).

Not borrowed: building your own tracing UI, prompt A/B platform, auto-promotion of winners.

### P6 Semantic cache (TypeScript, optional)
Checkpoints (5, same count):
1. Cache in front of the model (through the P2 gateway if it exists): exact match first, then semantic match, otherwise call and store.
2. Labelled set of paraphrase pairs and near-miss pairs. *Unchanged.*
3. **Threshold from a curve:** hit rate and false-hit rate at 4+ thresholds (was 2+). Also check a sample of hits, by hand or with P5's calibrated judge, to estimate the false-hit rate. Warmstart's target is under 1%; treat it as an aspiration. Source: Warmstart.
4. Hit rate, latency on hit against miss, and cost saved reported; provider prompt caching reported separately. *Unchanged.*
5. **Cache key and invalidation** (replaces "scoped per user or tenant"): key includes model, temperature, tool set, tenant and system-prompt version; TTL; tests showing no cross-tenant hit and that a system-prompt change busts the cache. Source: Warmstart.

Gotcha: the guides' thresholds are cosine similarity (higher is stricter); RedisVL's threshold is a distance (lower is stricter). Thresholds do not transfer between embedding models.
Not borrowed: Prometheus/Grafana, adaptive thresholds, OpenAI-compatible drop-in proxy, streaming buffering. The "30-60% savings" and "six in ten questions repeat" figures are scenarios, not results. Measure on your own replay.

### P7 Text-to-SQL (TypeScript, optional)
- **Question set (30-50):** include about 5 ambiguous questions where the right behaviour is a clarifying question (for example "revenue": gross or net) and about 5 unanswerable ones; score the expected behaviour. Source: Text-to-SQL (15).
- **Adversarial checkpoint:** use P2's attack-row format; report attack success by family and the false-block rate on benign questions. Avoid absolute claims such as "zero unsafe queries"; show instead that a write fails at the database level.
- **Optional:** generate two independent SQL attempts and use agreement as a confidence signal, but only if it predicts correctness on your own set.
- Keep the database as the boundary (read-only role, timeout, row limit). The guide treats the parser as the main defence and database permissions as the backup; I would not.
- Score execution match, not SQL text match.
- Not borrowed: schema relevance filter (the sample schema is small), EXPLAIN-based cost cap, subquery-depth rule.

---

## 7. Skipped projects (9)

- **LLM Cost Autopilot (15):** as written, every request is re-run on the top model for verification, so the "% saved" headline ignores the verifier's cost unless verification is sampled.
- **Failure Forensics (15):** rebuilds tracing, which Langfuse covers. Keep its failure taxonomy (extraction hallucination, misclassification, propagation error, prompt failure, context loss) as labels for error analysis.
- **Output Arbitration (15):** claims multi-model critics beat self-evaluation with no baseline. Judge calibration is the more rigorous version.
- **Prompt Versioning and A/B (15):** Langfuse covers prompt management and experiments (resource map, sections 7 and 12.4).
- **AI Feature Flags (15):** runs a judge on every response and rolls back on thin statistics; commodity feature-flag tools exist.
- **Self-Healing Docs (15):** distinct and fun, but no match to the target roles.
- **Fieldnote, multimodal RAG (short):** needs image and audio encoders; only if a target role asks for multimodal.
- **Knowledge Graph RAG (six):** 3-4 weeks, Neo4j, ontology design and entity resolution. "The gap widens with hop count" is an expected shape, not a result.
- **Multimodal Document Processor (15):** two OCR engines, Celery and a review UI. Vision-capable models can read pages directly. That is my opinion, not verified.

## 8. Context only: job posting and capstone

**The target posting (`resources/job-postings.md`, not tracked)**
- "AI gateways and model routing" (must-have, listed as a gap) is covered by gateway-lite.
- "Structured output, function calling, streaming, token and cost control" is partly covered: schema-validated output and cost per call. Streaming is not covered.
- "Eval frameworks (LLM-as-a-judge)" (nice-to-have) is covered by judge calibration.
- The attack test matches no line item directly, but the posting includes agentic, tool-using workflows with MCP.
- Still uncovered by any project: the 1+ year production requirement, browser automation, Cloudflare services, workflow engines.

**Capstone (`capstone/capstone.md`, not tracked in this repo)**
- M4 gateway overlaps gateway-lite; the module could be reused.
- M6 evals in CI overlaps the P5 CI gate and judge calibration.
- The capstone's untrusted tender documents are the same kind of attack surface as the attack test.

## 9. Verification notes

- All guide claims are the authors' assertions. Savings and accuracy figures are illustrative.
- Time estimates conflict: 12-14 days at 2-3 h/day (15 guide), 2-5 weeks at 10-12 h/week (six guide), 1-2 weekends (short guide).
- The guides contradict themselves: golden sets must be hand-written and "not generated by an LLM" (Model Regression Detection, Groundtruth), yet the Eval Dataset Generator labels cases automatically with GPT-4o.
- The 15 guide uses GPT-4o and GPT-4o mini throughout. OpenAI is reported to retire them from the API on 2027-01-14 (The New Stack; aiengineerguide, both Secondary). Check OpenAI's deprecations page. The Llama 3 8B and Mistral 7B picks are also dated.
- LiteLLM compromise: versions 1.82.7 and 1.82.8, 2026-03-24, several security vendors agree (Secondary). A clean 1.83.0 was released through a new pipeline.
- Ragas is reported to have no commits since 2026-02-24 (Techsy, 2026-08-04; ecorpit; both Secondary). I could not open GitHub to confirm. DeepEval is reported at v4.3.1 (2026-10-16, Secondary). The resource map (section 3) and the target posting mention RAGAS.
- garak v0.17.1, PyPI upload 2026-10-16 (Secondary). The Microsoft repo is `microsoft/PyRIT`; an `Azure/PyRIT` stub exists (Secondary).
- vLLM LoRA serving (`--enable-lora`, `--lora-modules`, dynamic load endpoints) is in the vLLM docs (Primary).
- Effort estimates in sections 5 and 6 are mine.
- Nothing here was tested hands-on.

## 10. Open questions

1. ~~P1 language~~ Resolved 2026-11-17: P1 is TypeScript. The P1 items in section 6 apply unchanged.
2. **Where the gateway module lives:** in the P2 repo. Later repos copy it or install it from git.
3. **P7 SQL parsing:** the resource map names sqlglot, which is Python. A TypeScript parser option is unchecked; otherwise the validator could be a small Python service.
4. ~~Applying this~~ Resolved 2026-11-19: checkpoints applied to `progress.md`; task plans written for P2 and P5.

## 11. Sources (accessed 2026-11-18)

- LiteLLM compromise (Secondary): https://bleepingcomputer.com/news/security/popular-litellm-pypi-package-compromised-in-teampcp-supply-chain-attack · https://letsdatascience.com/news/litellm-breach-exposes-ai-supply-chain-risk-69f3c6f7
- GPT-4o API retirement (Secondary): https://thenewstack.io/openai-gpt4o-api-shutdown · https://aiengineerguide.com/tools/model-retirements/gpt-4o/
- vLLM LoRA adapters (Primary): https://docs.vllm.ai/en/v0.14.0/features/lora/
- Eval framework status (Secondary): https://ecorpit.com/rag-evaluation-tools-ragas-deepeval-braintrust-giskard-2026/ · https://techsy.io/zh-tw/blog/llm-evaluation-framework-open-source
- garak (Secondary): https://wikidocs.net/470853
- PyRIT repo note (Secondary): https://app.stationx.net/articles/pyrit-microsoft
