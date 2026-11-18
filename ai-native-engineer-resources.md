# Becoming a 10x AI-Native Engineer: Resource Map

Compiled 2026-11-15. Purpose: learning resources for becoming an AI engineer and working effectively with AI. Organised around five portfolio projects transcribed from a "5 REAL projects" TikTok post by algorithmswithpeter (screenshot: `images/5_real_projects_for_AI_engineers.png`). The post's own advice: deploy the projects, measure results, showcase them on GitHub. Treat the project list as a skeleton.

**Updated 2026-11-17:** added a second source post (six project cards by bashi_fuirkashi, "one skill = one project"). Nothing above or below was removed. New material is in sections 11 to 13, plus two rows in the section 0 table and one line in the project order.

**Updated 2026-11-19:** added a third source, three PDF project guides (section 14; full extract in `references/guides-extract.md`). They add checkpoints, not projects.

**Source trust labels**
- **Primary** = official docs, vendor engineering blogs, original authors, publishers.
- **Secondary** = third-party blogs, aggregators, roundups. Useful for discovery; verify before relying on specifics.

**Suggested project order (opinion, not from a source):** 1 RAG, then 2 Agent + MCP, then 5 Evals (start the eval set during project 1), then 4 Self-host, then 3 Fine-tune (optional; try prompting or RAG first). **Added 2026-11-17:** projects 6 (semantic caching) and 7 (text-to-SQL with guardrails) are optional extensions from the second post; suggested slot is after 5 (see section 11.2).

---

## 0. Quick map: project to resources

| # | Project (from post) | Start here |
|---|---|---|
| 1 | Production RAG (hybrid search, reranking, citations) | Anthropic contextual retrieval cookbook; Chip Huyen, *AI Engineering* |
| 2 | Agent + MCP server | Anthropic "Building effective agents"; HF Agents course; HF MCP course; MCP spec |
| 3 | Fine-tune an LLM (LoRA/QLoRA) | HF LLM Course (LoRA chapter); Unsloth guide |
| 4 | Self-host an LLM (vLLM) | vLLM docs (quickstart, benchmarking) |
| 5 | AI evaluation pipeline (accuracy, latency, cost, security) | Hamel Husain evals; Anthropic "Demystifying evals"; Langfuse |
| 6 | Semantic caching layer (second post, optional) | RedisVL SemanticCache guide; Anthropic prompt caching docs (a different thing) |
| 7 | Text-to-SQL with guardrails (second post, optional) | Parse-and-validate plus a read-only DB role; OWASP LLM Top 10; BIRD paper for the metric |
| - | Working faster with AI coding agents | Claude Code best practices; Simon Willison's Agentic Engineering Patterns; Claude Academy |
| - | Backend and system design gaps | *Designing Data-Intensive Applications*, 2nd ed. |

---

## 1. Working with AI coding agents (the "10x" part)

- **Best practices for Claude Code** (Primary). Core idea: the context window fills fast and performance degrades as it fills; most advice follows from that. https://code.claude.com/docs/en/best-practices
- **Simon Willison, Agentic Engineering Patterns** (Primary, author). Free, growing guide (started Feb 23, 2026; about 11 chapters when last seen) on getting reliable, reviewable code from coding agents such as Claude Code and Codex. Chapters include "Writing code is cheap now", "Anti-patterns", "Red/green TDD", "Agentic manual testing". https://simonwillison.net/guides/agentic-engineering-patterns/
- **Claude Academy (formerly Anthropic Academy)** (Primary for Skilljar pages). Free courses with certificates. Developer-relevant ones seen: Claude Code 101, Claude Code in Action, Introduction to Agent Skills, Introduction to Subagents, Introduction to MCP, MCP Advanced Topics, Building with the Claude API, The AI-Native SDLC Playbook.
  - Catalog (Skilljar): https://anthropic.skilljar.com/
  - Claude Code 101: https://anthropic.skilljar.com/claude-code-101
  - Introduction to Agent Skills: https://anthropic.skilljar.com/introduction-to-agent-skills
  - Claude platform / agents course (agent loop, tool use, built-in tools, Skills, MCP, context management, Managed Agents): https://anthropic.skilljar.com/claude-platform-101
  - Course lengths and the full list come from a third-party tracker (Secondary): https://pasqualepillitteri.it/en/news/371/anthropic-academy-free-courses-claude (reports Claude Code 101 about 1.5 hours, Agent Skills about 1 hour, Subagents about 45 minutes, AI-Native SDLC Playbook about 1 hour; checked Nov 2026).

---

## 2. Building agents: patterns, context engineering, harnesses (Anthropic engineering blog, Primary)

Read roughly in this order:
1. Building effective agents (Dec 19, 2024). Workflows vs agents; prefer simple composable patterns. https://www.anthropic.com/engineering/building-effective-agents
2. Writing tools for agents (Sep 11, 2025). https://www.anthropic.com/engineering/writing-tools-for-agents
3. Effective context engineering for AI agents (Sep 29, 2025). Smallest set of high-signal tokens; just-in-time retrieval; sub-agents for long tasks. https://anthropic.com/engineering/effective-context-engineering-for-ai-agents
4. Effective harnesses for long-running agents (Nov 26, 2025). Agents working across sessions with no memory of earlier ones. https://anthropic.com/engineering/effective-harnesses-for-long-running-agents
5. Demystifying evals for AI agents (Jan 9, 2026). https://anthropic.com/engineering/demystifying-evals-for-ai-agents
6. Harness design for long-running application development (Mar 24, 2026). https://www.anthropic.com/engineering/harness-design-long-running-apps
7. Scaling Managed Agents (Apr 8, 2026). https://www.anthropic.com/engineering/managed-agents

(Dates for items 2, 6, 7 come from an RSS mirror of the blog: https://rss-parrot.net/web/feeds/www.anthropic.com.engineering. Secondary, but the post URLs are Anthropic's.)

---

## 3. Project 1: Production RAG

- **Anthropic, Contextual Retrieval** (Primary). Prepend LLM-generated, chunk-specific context before embedding and BM25 indexing. https://www.anthropic.com/news/contextual-retrieval
- **Cookbook: Enhancing RAG with contextual retrieval** (Primary). Walks through basic RAG, contextual embeddings, contextual BM25, reranking. Needs intermediate Python; the guide estimates about $5-10 in API costs for the full dataset. https://platform.claude.com/cookbook/capabilities-contextual-embeddings-guide
- **Reported results** (Anthropic's own benchmarks, relayed by a Secondary glossary): contextual embeddings plus contextual BM25 cut retrieval failures by about 49%; adding reranking brings it to about 67%. Measure on your own data. https://memx.app/glossary/contextual-retrieval/
- **Chip Huyen, *AI Engineering* (O'Reilly, Dec 2024)** (Primary, publisher). Covers building applications with foundation models; table of contents includes a section comparing AI engineering with full-stack engineering. Companion repo: https://github.com/chiphuyen/aie-book . Book page: https://www.oreilly.com/library/view/ai-engineering/9781098166298/
- **Ragas**, open-source RAG evaluation library (Secondary mention in Louis Bouchard's roadmap, section 9). No direct URL was verified in this research.

---

## 4. Project 2: Agent + MCP server

- **Hugging Face Agents Course** (Primary). Free, certified; covers agents, tools, orchestration with open models. https://huggingface.co/agents-course
- **Hugging Face MCP Course** (Primary). Free; beginner-to-practitioner MCP. https://huggingface.co/learn/mcp-course/unit0/introduction . A Secondary review estimates 10-15 hours and a focus on building production MCP servers: https://aiagentrank.io/learn/course/huggingface-mcp-course
- **MCP specification** (Primary). The official docs index lists revision **2026-07-28** as latest. https://modelcontextprotocol.io/specification/latest . Official blog post for that revision: https://blog.modelcontextprotocol.io/posts/2026-07-28/
  - Secondary sources say this revision makes the protocol stateless, with per-request version and capability metadata: https://modelcontextprotocol.info/specification/ . Check the official changelog before relying on this.
  - One third-party tutorial claims a new `@modelcontextprotocol/server` v2 package alongside the older v1 SDK. **Unverified**; check the official SDK docs: https://mcpplaygroundonline.com/blog/build-mcp-server-2026-spec.md
  - Many MCP tutorials predate this revision, so check which spec version a tutorial targets.
- Also see section 2 (Anthropic agent posts).

---

## 5. Project 3: Fine-tune an LLM (LoRA / QLoRA)

- **Hugging Face LLM Course, LoRA chapter** (Primary). Fine-tune with TRL's SFTTrainer plus PEFT; LoRA keeps base weights frozen and makes consumer-GPU fine-tuning possible. Start with small ranks (4-8), watch training loss, use a validation set. https://huggingface.co/learn/llm-course/en/chapter11/4
- **Unsloth fine-tuning guide** (Primary, docs). Beginner-friendly best practices. https://docs.unsloth.ai/get-started/fine-tuning-guide
- **Hardware** (Secondary): one review says free Colab GPUs are sufficient for models up to about 3B parameters with LoRA/QLoRA (https://aiagentrank.io/learn/fine-tuning-llms ); other posts claim 7B on a free T4 with Unsloth, so claims vary. Test on your target model.
- **When to fine-tune** (Secondary): a 2026 guide advises trying few-shot prompting or RAG before fine-tuning. https://www.kunalganglani.com/blog/fine-tune-open-source-llm-lora-qlora

---

## 6. Project 4: Self-host an LLM with vLLM

- **vLLM docs** (Primary). Quickstart covers offline batched inference and the OpenAI-compatible server; requires Linux. https://docs.vllm.ai/
- **Benchmarking**: `vllm bench serve` measures throughput and latency (TTFT, TPOT). Docs page (versioned, v0.12.0, so check the current version): https://docs.vllm.ai/en/v0.12.0/benchmarking/cli/ . Skill-style summary of the command (Secondary): https://skills.sh/vllm-project/vllm-skills/vllm-bench-serve
- You will likely need a GPU machine or a rented GPU.

---

## 7. Project 5: Evals and observability

**Evals**
- **Hamel Husain, "Your AI Product Needs Evals"** (Primary, author; Mar 29, 2024). https://hamel.dev/blog/posts/evals/
- **Free 17-part email course** by Hamel Husain and Shreya Shankar (Primary); includes two free e-books (an evals FAQ and an advanced RAG optimisation and evals guide). https://ai.hamel.dev/eval-course
- **Book: *Evals for AI Engineers*** (Shankar and Husain, O'Reilly, Oct 2026; Primary, publisher). https://www.oreilly.com/library/view/evals-for-ai/9798341660717/
- **Paid course: AI Evals for Engineers & PMs** (Maven; Primary). https://maven.com/parlance-labs/evals
- **Simon Willison on the evals FAQ** (Secondary summary; notes evaluation is part of the development process, and that a 100% pass rate may mean your evals are too easy). https://www.simonwillison.net/2025/Jul/3/faqs-about-ai-evals
- **The Pragmatic Engineer: evals deep dive with Hamel Husain** (Secondary). https://newsletter.pragmaticengineer.com/p/evals
- **Anthropic: Demystifying evals for AI agents**: see section 2.

**Observability / tracing**
- **Langfuse** (Primary). Open-source platform for tracing, evals, prompt management, datasets; OpenTelemetry-based; MIT core, self-hostable. Reported to have joined ClickHouse in 2026 (Secondary: https://www.digitalocean.com/resources/articles/langfuse-alternatives ), so check its roadmap. https://langfuse.com/ . OTel integration: https://langfuse.com/integrations/native/opentelemetry.md
- **Alternatives** (Secondary, same DigitalOcean article): Arize Phoenix (OTel-native, Elastic License 2.0), Laminar, MLflow.

---

## 8. Backend and system-design gaps

- **Designing Data-Intensive Applications, 2nd ed.** (Kleppmann and Riccomini, O'Reilly, March 2026; Primary). https://martin.kleppmann.com/2026/03/24/designing-data-intensive-applications-2e.html . Publisher page: https://www.oreilly.com/library/view/-/9781098119058

---

## 9. Staying current and broader roadmaps

- **Latent Space** (Primary): newsletter, podcast and community for AI engineers. https://latent.space/ . AI Engineer World's Fair 2026 recap: https://latent.space/p/aiewf26trends . Event and talks hub: https://ai.engineer/
- **Simon Willison's blog** (Primary): practical LLM experiments, security, agent risk. https://simonwillison.net/
- **Louis-Francois Bouchard, Start AI Engineering** (Secondary, aggregator): free GitHub roadmap covering RAG, agents, evals, deployment, security. Use as a checklist to find gaps. https://github.com/louisfb01/start-ai-engineering . Companion page: https://www.louisbouchard.ai/learn-ai-engineering/
- **Other roadmaps** (Secondary, discovery only):
  - https://github.com/vishalgwu/ultimate-ai-engineering-roadmap-2026
  - https://dataskew.io/roadmaps/ai-engineering/
  - https://www.firecrawl.dev/blog/best-ai-resources
  - https://technologiee.substack.com/p/how-to-become-an-ai-engineer-in-2026
- **Other free agent courses** named in a Secondary roundup (no direct URLs verified): Microsoft "AI Agents for Beginners" (GitHub), Google and Kaggle "5-Day AI Agents Intensive", LangChain Academy's LangGraph course. https://www.masaischool.com/blog/free-agentic-ai-courses-and-resources/

---

## 10. Verification notes and open questions

- I could not open academy.claude.com directly. Secondary sources say Anthropic Academy moved there in 2026 and renamed to Claude Academy; the Skilljar catalog URL above did appear in search results. Course counts vary across sources (13 to 28), so check the live catalog.
- Anthropic's retrieval-improvement figures are self-reported benchmarks.
- MCP spec statelessness and SDK package details come partly from third-party pages (see section 4).
- Prices, dates and course lengths from Secondary sources may be stale.
- Nothing here was tested hands-on; all findings are from web search results as of 2026-11-15.

---

## 11. Second post (added 2026-11-17): "one skill = one project"

**Source:** a post by bashi_fuirkashi, "One Skill = One Project", shown as six project cards in screenshots. The six screenshots are saved as `images/six_ai_skills_projects_1.png` to `images/six_ai_skills_projects_6.png`; this section is the text record. The post appears to promote a paid accelerator program; I noted that when transcribing and did not check the program or its terms. Which skill each card belongs to (RAG, evals, fine-tuning, guardrails and so on) is inferred from the card titles, and any partly obscured text is a best reading. Treat the cards as project ideas, not a curriculum. The stacks are the poster's choices, not requirements. The card steps below are paraphrased.

### 11.1 How the six cards map onto the existing plan

| Card | Maps to | Relationship | What to do with it |
|---|---|---|---|
| Hybrid Search RAG over Internal Docs | P1 | Same project, different stack | Variant. Keep the local stack in `tasks/project-1-rag.md`; the cloud stack is an optional rebuild |
| Agent Orchestration System | P2 | Overlaps; adds backend-heavy parts | Variant. Combine with the MCP server from P2 |
| Semantic Caching Layer | new P6 | New | Optional extension |
| Automated Eval Dataset Generator | P5 | A component of P5 | Fold into P5 |
| LoRA Fine-Tuning Pipeline | P3 | Same project, adds W&B tracking | Variant |
| Text-to-SQL with Guardrails | new P7 | New | Optional extension |

The first post's "self-host with vLLM" (P4) is not in the second post. Combined, the two posts cover seven distinct skills: RAG, agents, evals, fine-tuning, self-hosting, caching, guardrails.

### 11.2 Recommended way to include them (opinion)

- **Keep the numbering P1 to P5.** `progress.md`, `tasks/` and `resources/job-postings.md` (not tracked) all refer to those numbers.
- **Add P6 and P7 as optional extensions**, and record the other four cards as variants inside P1, P2, P3 and P5 rather than as new projects. Doing both versions of one skill adds little.
- **Suggested order:** 1, 2, 5, then 6, then 7, then 4, then 3. P6 needs P5's eval and cost tracking to catch wrong cache hits and prove savings. P7 needs the error-analysis habit from P1 and P5.
- **Fit with the target posting** (`resources/job-postings.md`, not tracked): P6 helps with the listed gap "PostgreSQL and caching (Redis, KV)" and with cost control. P7 helps with Postgres and with fallback flows for bad model output. Neither replaces the posting's production-experience requirement. The posting is TypeScript end to end, and the cards are Python. Language is your choice, so P6 could be built in TypeScript.
- **Order of the task plans:** `tasks/` holds P1, P2 and P5 (P2 and P5 added 2026-11-19). Add P6 and P7 one at a time, in the same format.

---

## 12. Card details, notes and resources

### 12.1 Hybrid Search RAG over Internal Docs (variant of P1)

- **Card stack:** Python, LangChain, Claude API, Pinecone, rank_bm25, Cohere Rerank, FastAPI.
- **Steps (paraphrased):** chunk and embed the documents into Pinecone; build a separate BM25 index over the same corpus; run both retrievers and merge with reciprocal rank fusion; rerank the merged set with Cohere and keep the top k; answer with inline citations and check each claim maps to a source.
- **Notes:** this is the same pipeline as P1 tasks 6 to 12. The difference is a managed vector database and a hosted reranker, which need accounts and keys; free-tier terms were not checked. The local stack in the P1 plan needs none.
- **Resources:**
  - Pinecone on reciprocal rank fusion (Primary): RRF is a client-side method that fuses rankings instead of raw scores. https://docs.pinecone.io/guides/search/reciprocal-rank-fusion
  - Pinecone hybrid search overview (Primary): notes that RRF is one fusion method, not a synonym for hybrid search. https://docs.pinecone.io/docs/hybrid-search
  - Pinecone research, "An analysis of fusion functions for hybrid retrieval" (Primary, vendor research): compares convex-combination fusion with RRF. https://www.pinecone.io/research/an-analysis-of-fusion-functions-for-hybrid-retrieval/
  - Cohere Rerank docs: URL not verified in this research.

### 12.2 Agent Orchestration System (variant of P2)

- **Card stack:** Python, LangGraph, Claude API, Redis, Postgres, FastAPI, Celery.
- **Steps (paraphrased):** define an agent graph with plan, act and reflect nodes; register every tool with a typed input schema; persist memory in Redis between steps; add a human approval checkpoint for high-risk actions; log every step so failures can be replayed and debugged.
- **Notes:** this variant targets your backend gaps (queue, persistence, approval flow). It has no MCP part, so use an MCP server as the tool layer to cover P2. Pushback: a plan/act/reflect graph can be overkill for a first agent. Anthropic's guidance (section 2, item 1) is to start simple and add structure only when needed, so start with a plain tool-calling loop and move to a graph when state and branching require it.
- **Resources:**
  - LangGraph persistence (Primary): a checkpointer saves a snapshot of graph state at every step, which enables human-in-the-loop, memory, time-travel debugging and fault tolerance. https://docs.langchain.com/oss/python/langgraph/persistence.md
  - LangGraph human-in-the-loop (Primary): `interrupt()` pauses the graph, saves state, and waits for you to resume; it needs a checkpointer. https://docs.langchain.com/oss/python/langgraph/human-in-the-loop
  - LangGraph checkpointers (Primary). https://docs.langchain.com/oss/python/langgraph/checkpointers
  - Redis as the checkpointer, as on the card, was **not verified** in the docs I found. A database-backed checkpointer is the documented production direction, so Postgres (already on the card) is the safer choice. Check the current checkpointer list first.
  - Replaying failures: trace each step with Langfuse (section 7).

### 12.3 Semantic Caching Layer (new P6, optional)

- **Card stack:** Python, FastAPI, Redis, OpenAI embeddings, FAISS, Claude API.
- **Steps (paraphrased):** embed each incoming prompt before it reaches the model; search the cache for a semantically similar earlier prompt; above a similarity threshold return the cached answer; on a miss call the model and store the embedding with the response; track hit rate and money saved on a simple dashboard.
- **Notes and pushback:**
  - Redis and FAISS are both vector stores. Pick one. RedisVL's `SemanticCache` already includes thresholds and TTL; FAISS is a library without expiry.
  - The card embeds with OpenAI for a Claude project. A local embedding model avoids a second vendor key, as in the P1 plan.
  - The main risk is wrong hits. A looser threshold raises the hit rate and the wrong-answer rate together. Build a labelled set of paraphrase pairs and near-miss pairs (similar wording, different correct answer) and measure the false-hit rate before reporting savings.
  - Scope the cache per user, tenant and system prompt, or answers can leak across users. RedisVL's guide covers tags and filters for multi-user access control.
  - Do not confuse this with provider-side prompt caching. Report the two savings separately.
- **Resources:**
  - RedisVL LLM caching guide (Primary, versioned page; 0.14.0 was the newest seen): covers `SemanticCache`, the distance threshold (cosine, lower is stricter), TTL policies and access controls. https://redis.io/docs/latest/develop/ai/redisvl/0.14.0/user_guide/llmcache/
  - Anthropic prompt caching (Primary): caches the prompt prefix; cache reads cost 0.1x the base input price; a 5-minute lifetime with a 1-hour option. https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching . A newer page on platform.claude.com also describes automatic caching with a single top-level `cache_control` field.
- **Evidence to record:** hit rate, false-hit rate on the labelled pairs, latency on hit vs miss, and cost saved.

### 12.4 Automated Eval Dataset Generator (component of P5)

- **Card stack:** Python, Claude API, Pandas, Pydantic, DuckDB, Streamlit.
- **Steps (paraphrased):** pull real prompts and responses from production logs; cluster them to surface the cases that matter; have an LLM draft expected outputs, then review them by hand; score each new model version against the set automatically; flag regressions before release.
- **Notes:** you have no production logs, so use the traces from P1 and P2 as the log source. Keep the hand-review step. The note in the P1 plan (task 5) applies: generated cases tend to copy the source wording and flatter the system. Build the dataset from observed failures, as in Hamel Husain's error-analysis approach.
- **Resources:** Hamel Husain's essay and email course, Anthropic's "Demystifying evals" (sections 2 and 7). Langfuse lists tracing, evals, prompt management and experiments (https://langfuse.com/); check its dataset features before choosing it over a plain script.

### 12.5 LoRA Fine-Tuning Pipeline (variant of P3)

- **Card stack:** Python, PyTorch, Hugging Face, PEFT, bitsandbytes, Weights & Biases.
- **Steps (paraphrased):** build a clean instruction dataset for your domain; load the base model in 4-bit with bitsandbytes; attach LoRA adapters with PEFT and set rank and alpha; train while tracking loss and eval metrics in W&B; merge the adapters and benchmark against the base model.
- **Notes:** this matches section 5. The "benchmark against the base model" step is the part that matters; run it on the same eval set as the prompting baseline. W&B is optional and was not researched. Confirm bitsandbytes works on your hardware before committing (not checked here).
- **Resources:** section 5.

### 12.6 Text-to-SQL with Guardrails (new P7, optional)

- **Card stack:** Python, Claude API, SQLAlchemy, Postgres, sqlglot, Pydantic, FastAPI.
- **Steps (paraphrased):** give the model the schema and the question; parse the generated SQL with sqlglot and block destructive statements; run it read-only against a sandboxed replica first; attach a confidence score and ask for confirmation on low-confidence queries; return the results together with the SQL so users can audit it.
- **Notes and pushback:**
  - Treat model-generated SQL as untrusted input. A parse-and-block step is one layer, not the boundary. The boundary is the database: a read-only role, a statement timeout, row limits, and row-level security where it applies.
  - Validate the exact string you execute. If you normalise or strip comments after validating, you may run something different from what you checked.
  - Self-reported confidence scores are only useful if you check them against measured accuracy on your own question set.
  - Prove the guardrails: write adversarial prompts (drops, updates, multi-statement input, hidden columns) and show the database refuses writes even if the parser is bypassed.
- **Resources:**
  - OWASP Top 10 for LLM Applications 2025 (Secondary summary): lists Improper Output Handling (LLM05) and Excessive Agency (LLM06) among the risks. https://www.hackerone.com/ai/owasp-top-10-llms-2025 . The official OWASP GenAI Security Project page was not opened, and a 2026 edition may exist, so check it.
  - sql-guardrail (Secondary, a third-party package, listed as an example of policy-based validation, not an endorsement): sqlglot-based checks, with advice to pair it with a read-only role, row-level security and a statement timeout. https://pypi.org/project/sql-guardrail/
  - BIRD benchmark paper (Primary, research): defines execution accuracy (EX) and a valid efficiency score; use EX as the metric on your own question set. https://arxiv.org/pdf/2305.03111
  - sqlglot docs: URL not verified in this research.
- **Evidence to record:** execution accuracy on a 30 to 50 question set over a sample Postgres database; adversarial prompts blocked; false blocks; proof that a write attempt fails at the database level.

---

## 13. Verification notes for the 2026-11-17 additions

- The card contents come from screenshots of a social post. I have not seen the full post or its caption, and the card-to-skill mapping is inferred.
- That the post promotes a paid program is an impression from the screenshots, not verified.
- The Pinecone, LangGraph, RedisVL and Anthropic prompt caching pages are official docs, and several are versioned or in alpha or beta, so re-check them before building. The LangGraph human-in-the-loop page seen in one search result carried a v1-alpha notice.
- OWASP details come from a Secondary summary. The sql-guardrail and OWASP entries are for orientation only.
- The BIRD paper is from 2023. I did not use its benchmark scores, which are stale.
- Nothing here was tested hands-on.

---

## 14. Third source (added 2026-11-19): three PDF project guides

**Source:** three PDF guides of AI-engineering project specs (a 9-page short guide, a 21-page six-project guide, a 50-page 15-project guide), kept outside this repo. All three promote one paid online community; no citations or code, and every number in them is illustrative. The full extract, the sorting of their 27 projects and the reasoning is in `references/guides-extract.md`.

**What was taken (applied 2026-11-19):**
- Three additions, as checkpoints inside existing projects: **gateway-lite** (P2), **attack your own agent** (P2), **judge calibration** (P5). Task plans: `tasks/project-2-agent-mcp.md`, `tasks/project-5-evals.md`.
- Borrowed checkpoints for every project: MRR and per-category Pass@k and a citation-support check in P1; budget ceiling and structured tool status in P2; a CI gate with flip lists and an error-analysis-to-dataset loop in P5; threshold curves and cache-key invalidation in P6; ambiguous and unanswerable questions in P7; held-out sets and break-even numbers in P3 and P4. See `progress.md`.
- Shared advice worth keeping: a project counts only with an eval, a number and a defensible tradeoff; build the eval before the feature; the README opens with the result.

**Tools named in the extract** (Secondary unless marked, accessed 2026-11-18):
- garak (NVIDIA, generic LLM vulnerability probes; v0.17.1 reported 2026-10-16) and PyRIT (Microsoft, multi-turn attacks; repo `microsoft/PyRIT`). Both Python. Use only against your own systems. Optional for P2 task 11.
- DeepEval: reported at v4.3.1 (2026-10-16). Python. **Ragas** (section 3) is reported to have had no commits since 2026-02-24; not confirmed on GitHub, so check before adopting it.
- vLLM LoRA serving, `--enable-lora` (Primary): https://docs.vllm.ai/en/v0.14.0/features/lora/ (for P3 on P4).
- **Caution:** LiteLLM 1.82.7 and 1.82.8 (2026-03-24) were malicious releases; 1.83.0 is reported clean. Pin and hash-verify if a Python project uses it.
