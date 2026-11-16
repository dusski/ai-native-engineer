# Becoming a 10x AI-Native Engineer: Resource Map

Compiled 2026-11-15. Purpose: learning resources for becoming an AI engineer and working effectively with AI. Organised around five portfolio projects transcribed from a "5 REAL projects" TikTok post by algorithmswithpeter (screenshot: `images/1000043345.png`). The post's own advice: deploy the projects, measure results, showcase them on GitHub. Treat the project list as a skeleton.

**Source trust labels**
- **Primary** = official docs, vendor engineering blogs, original authors, publishers.
- **Secondary** = third-party blogs, aggregators, roundups. Useful for discovery; verify before relying on specifics.

**Suggested project order (opinion, not from a source):** 1 RAG, then 2 Agent + MCP, then 5 Evals (start the eval set during project 1), then 4 Self-host, then 3 Fine-tune (optional; try prompting or RAG first).

---

## 0. Quick map: project to resources

| # | Project (from post) | Start here |
|---|---|---|
| 1 | Production RAG (hybrid search, reranking, citations) | Anthropic contextual retrieval cookbook; Chip Huyen, *AI Engineering* |
| 2 | Agent + MCP server | Anthropic "Building effective agents"; HF Agents course; HF MCP course; MCP spec |
| 3 | Fine-tune an LLM (LoRA/QLoRA) | HF LLM Course (LoRA chapter); Unsloth guide |
| 4 | Self-host an LLM (vLLM) | vLLM docs (quickstart, benchmarking) |
| 5 | AI evaluation pipeline (accuracy, latency, cost, security) | Hamel Husain evals; Anthropic "Demystifying evals"; Langfuse |
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
