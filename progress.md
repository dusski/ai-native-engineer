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
- [ ] Hybrid search (vector + BM25) added [retrieval hit-rate vs baseline]
- [ ] Reranking added [hit-rate vs hybrid]
- [ ] Answers carry citations [example output]
- [ ] Short write-up with before/after numbers [README link]

Repo / links: `projects/rag-hybrid-search` (own git repo, local only, not pushed). `main` = TypeScript; branch `python-version` keeps the earlier Python work.
Notes: Task 1 done (2026-11-18): Claude, local embedding and local rerank all work in `scripts/smoke-test.ts`. Next: task 2, choose the document set.

## Project 2: Agent + MCP server
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] MCP server exposing at least two tools [repo link; MCP spec version targeted]
- [ ] Agent completes a real multi-step task using those tools [demo or trace]
- [ ] Tool errors and timeouts handled [test or trace showing recovery]
- [ ] Short write-up of design choices [README link]
- [ ] (Optional, from second post) Human approval step for a risky action, with step logs you can replay [trace]

Repo / links: (none yet)
Notes: (none yet)

## Project 5: Eval and observability pipeline
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Traces captured for projects 1 and 2 [tracing tool + screenshot or link]
- [ ] Automated evals run from a command or CI [run output]
- [ ] Accuracy, latency and cost tracked per run [table or dashboard]
- [ ] At least one failure found through error analysis and fixed [before/after]
- [ ] (From second post) Eval dataset built from real traces, expected outputs reviewed by hand [case count + how many edited]
- [ ] (From second post) A new model or prompt version scored against the set, regressions flagged [run output]

Repo / links: (none yet)
Notes: (none yet)

## Project 6: Semantic caching layer (optional, from second post)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Cache in front of the model: embed, look up, return on hit, store on miss [repo link]
- [ ] Labelled set of paraphrase pairs and near-miss pairs [count + file location]
- [ ] Similarity threshold chosen from measurements [hit rate and false-hit rate at 2+ thresholds]
- [ ] Hit rate, latency (hit vs miss) and cost saved reported [numbers]
- [ ] Cache scoped per user or tenant [test showing no cross-user hit]

Repo / links: (none yet)
Notes: (none yet)

## Project 7: Text-to-SQL with guardrails (optional, from second post)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Question to SQL over a sample Postgres database [repo link]
- [ ] Generated SQL parsed and destructive statements blocked [test output]
- [ ] Execution accuracy measured on a 30 to 50 question set [score]
- [ ] Adversarial prompts tried (writes, multi-statement, hidden columns) [blocked count, false blocks]
- [ ] Writes fail at the database level (read-only role) even if the parser is bypassed [test output]
- [ ] Results returned with the SQL shown [example output]

Repo / links: (none yet)
Notes: (none yet)

## Project 4: Self-hosted LLM with vLLM
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] vLLM serving a model through the OpenAI-compatible endpoint [command + hardware used]
- [ ] Benchmark run: throughput, TTFT, TPOT recorded [numbers]
- [ ] One of projects 1 or 2 pointed at the self-hosted endpoint [result vs hosted API]
- [ ] Write-up with the numbers and hardware [README link]

Repo / links: (none yet)
Notes: (none yet)

## Project 3: LoRA/QLoRA fine-tune (optional, last)
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] Prompting/RAG baseline measured on the eval set first [score]
- [ ] LoRA or QLoRA run completes on a small model [training config + loss]
- [ ] Fine-tuned model compared with the baseline on the same eval set [scores]
- [ ] Decision recorded: was fine-tuning worth it for this task? [one paragraph]

Repo / links: (none yet)
Notes: (none yet)

---

## Side tracks (not projects)

**Working with AI coding agents**
- [ ] Claude Code best practices read and applied to one real repo
- [ ] Notes: (none yet)

**Backend and system-design gaps**
- [ ] Gaps to cover: (add as chosen)
- [ ] Notes: (none yet)

---

## Log (newest first)

- 2026-11-18: Project 1, task 1 done. Local embedding (`all-MiniLM-L6-v2-ONNX`, 384 dims, normalized, ~10–15 ms per embed) and local rerank spike passed (`Xenova/bge-reranker-base`, q8, 266 MB): relevant 7.18 vs non-answers 0.25 and 0.39, 3 pairs in ~124–141 ms, cached load ~700 ms. No hosted fallback needed. Findings: the transformers.js `text-classification` pipeline can't score pairs (use tokenizer `text_pair` + `AutoModelForSequenceClassification`), and Node defaults to fp32 unless `dtype: "q8"` is set. A run with a truly unrelated third chunk (CSS Grid) scored 6.88 / -0.26 / -10.18, so off-topic text lands far below near-topic non-answers.
- 2026-11-17: Project 1 repo re-initialised as TypeScript. Old Python work (3 commits) kept on branch `python-version`; `main` reset to the initial commit plus `46e7e2e` "Initialize TypeScript project with dependencies" (npm, tsconfig, `@anthropic-ai/sdk`, `@huggingface/transformers`, `dotenv`, `tsx`, `vitest`). `scripts/smoke-test.ts` ported from the Python version: typecheck clean, Claude reply printed, stop_reason `end_turn`. Not yet done: embedding and rerank spike. Watch item: npm skipped install scripts for `protobufjs` and `fsevents`; check this first if the ONNX runtime fails to load.
- 2026-11-17: Decision: Project 1 switches from Python to TypeScript, so the RAG concepts aren't learned alongside a new language (also matches the TypeScript-end-to-end target posting). Python stays for projects 3 and 4. Risk: local embeddings/reranker move from `sentence-transformers` to transformers.js (ONNX); task 1 now includes a spike, with a hosted-API fallback. Updated `tasks/project-1-rag.md` (stack, tasks 1, 3, 6, 7, 11 and file names). The Python scaffold in `projects/rag-hybrid-search` still needs replacing.
- 2026-11-17: Synced local files with the Claude Project export. Added the second source post to the resource map (sections 11 to 13), optional projects 6 (semantic caching) and 7 (text-to-SQL guardrails) and extra checkpoints for projects 2 and 5 to this tracker, and a new doc, `job-postings.md`. No local content was overwritten.
- 2026-11-16: Project 1, task 1 in progress. uv project and deps committed; smoke test calls Claude successfully (stop_reason `end_turn`). Embedding check still missing. First run hit a 400 "credit balance too low" error before it passed.
- 2026-11-16: Created the local repo `rag-hybrid-search` for Project 1 (inside `projects/`, not pushed to GitHub). No task done yet.
- 2026-11-16: Workspace cleanup. Moved the Project 1 plan to `tasks/project-1-rag.md`, fixed paths in all files, rewrote `AGENTS.md`. No project work started yet.
- 2026-11-15: Task plan for Project 1 added. No project work started yet.
- 2026-11-15: Project set up. Custom instructions written, resource map added (`ai-native-engineer-resources.md`), this tracker created. No project work started yet.
