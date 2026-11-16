# Progress Tracker: Becoming a 10x AI-Native Engineer

Resources for each project are in `ai-native-engineer-resources.md`. Step-by-step task plans live in `tasks/` (one file per project). This file tracks what is actually done. To update it, tell Claude (for example "record progress: RAG baseline works, 40 eval questions").

**Rules for keeping it honest**
- A checkpoint counts only when its evidence exists (demo link, eval result, benchmark number, trace).
- Record numbers, not feelings. Record what failed too.
- Update on milestones, not on every message. One milestone updates three places together: the task's `Result` line, the checkpoint and `Status` here, and the log.

**Status key:** Not started / In progress / Done

**Planned order (suggestion, can change):** 1, 2, 5, 4, 3. Start the eval set during project 1.

---

## Project 1: Production RAG (hybrid search, reranking, citations)
Status: Not started
Task plan: `tasks/project-1-rag.md` (16 tasks, 2 optional)

Checkpoints (evidence in brackets):
- [ ] Baseline RAG answers questions over a chosen document set [demo or repo link]
- [ ] Eval set of questions with expected sources [count + file location]
- [ ] Hybrid search (vector + BM25) added [retrieval hit-rate vs baseline]
- [ ] Reranking added [hit-rate vs hybrid]
- [ ] Answers carry citations [example output]
- [ ] Short write-up with before/after numbers [README link]

Repo / links: `projects/rag-hybrid-search` (own git repo, local only, not pushed)
Notes: (none yet)

## Project 2: Agent + MCP server
Status: Not started
Task plan: not written yet

Checkpoints:
- [ ] MCP server exposing at least two tools [repo link; MCP spec version targeted]
- [ ] Agent completes a real multi-step task using those tools [demo or trace]
- [ ] Tool errors and timeouts handled [test or trace showing recovery]
- [ ] Short write-up of design choices [README link]

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

- 2026-11-16: Created the local repo `rag-hybrid-search` for Project 1 (inside `projects/`, not pushed to GitHub). No task done yet.
- 2026-11-16: Workspace cleanup. Moved the Project 1 plan to `tasks/project-1-rag.md`, fixed paths in all files, rewrote `AGENTS.md`. No project work started yet.
- 2026-11-15: Task plan for Project 1 added. No project work started yet.
- 2026-11-15: Project set up. Custom instructions written, resource map added (`ai-native-engineer-resources.md`), this tracker created. No project work started yet.
