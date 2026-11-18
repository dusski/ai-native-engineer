# Weekend Track: Free AI Certifications — Task Plan

Source: "20 free AI certifications" post (@meghana.ai; screenshot: `images/20_free_ai_certifications.png`), added 2026-11-18. Curated from 20 down to 8 core plus 5 optional, ordered by importance and grouped by the project each one supports. Planned project order: 1, 2, 5, (6, 7), 4, 3. Skipped items and the reasons are at the bottom.

**How to use this file**
- One task per weekend: 2–3h on Saturday and 2–3h on Sunday (~5h max).
- **Postponing is allowed.** A missed weekend shifts everything after it by one week. Never double up to catch up. Mark the task `Postponed (to YYYY-MM-DD)`.
- Keep each phase roughly in step with the project you're on during the week. If you finish a phase early, start the next phase's courses. If you fall behind on the project, the weekend can go to project work and the course gets postponed.
- A task counts as Done only when the certificate or badge is issued (paste its URL in Result), or, for courses without one, when the "Done when" check passes.
- Status values: Not started / In progress / Done / Postponed / Skipped.
- Certificates carry little weight with hiring managers. These courses are structured learning that feeds the projects. The repos are the proof.

Dates are tentative. Time estimates come from provider pages and reviews, checked 2026-11-18. Estimates marked *(unverified)* are my guesses.

---

## TOC

**Phase 1: Project 1 (RAG): the most important courses**
1. [ ] Claude Code 101 + Claude Code in Action (Sat 21 Nov) + Building with the Claude API, start (Sun 22 Nov)
2. [ ] Building with the Claude API: through the RAG section (28–29 Nov)
3. [ ] Building with the Claude API: finish + certificate, and MongoDB Vector Search + RAG badges (5–6 Dec)

**Phase 2: Project 2 (Agent + MCP)**
4. [ ] Claude Academy Intro to MCP + HF MCP course Unit 1 certificate (12–13 Dec)
5. [ ] HF Agents course Unit 1 certificate (19–20 Dec)
   *(26–27 Dec and 2–3 Jan: holiday buffer, postpone freely)*

**Phase 3: Project 5 (Evals and observability)**
6. [ ] HF Agents Bonus Unit 2: Agent observability and evaluation (9–10 Jan 2027)

**Phase 4: Projects 4 and 3 (Python required)**
7. [ ] Kaggle Python (before starting Project 4)
8. [ ] Kaggle Intro to Deep Learning (before Project 3)

**Optional (only if the matching project needs it)**
9. [ ] HF MCP course: full completion (Units 2–3), during Project 2
10. [ ] HF Agents course: full completion, during or after Project 2
11. [ ] Neo4j & Generative AI certification, if you do a GraphRAG extension of Project 1
12. [ ] Databricks GenAI Fundamentals: filler badge for a spare weekend
13. [ ] CS50 AI (parked)

---

## Phase 1: Project 1 (RAG)

### 1. Claude Code courses + start Building with the Claude API — Not started
**Maps to:** side track "Working with AI coding agents"; Project 1
**Why first:** you build with heavy Claude help, so better Claude Code habits pay off on every later task.
**Description**
Sat: Claude Code 101 and Claude Code in Action (~1h each). Apply one practice to your Project 1 repo the same day, for example a CLAUDE.md, plan mode, or tests first. Sun: start Building with the Claude API, covering API basics and prompting.
**Link:** academy.claude.com (rebranded from anthropic.skilljar.com in Aug 2026)
**Time:** ~2h Sat + ~3h Sun
**Deliverables:** 2 certificates. Tick the side-track checkbox in `progress.md`.
**Result:**

### 2. Building with the Claude API: through RAG — Not started
**Maps to:** Project 1 (tasks 8, 12, 14)
**Description**
The flagship developer course has 84 lessons and 8+ hours of video. This weekend covers tool use, prompt caching and the RAG section. Run the examples yourself. Note anything that changes your Project 1 plan; prompt caching feeds task 14 (contextual retrieval).
**Time:** ~5h
**Deliverables:** Short notes appended to Project 1's README draft or `results.md`
**Result:**

### 3. Finish Building with the Claude API + MongoDB badges — Not started
**Maps to:** Project 1
**Description**
Sat: the remaining sections (MCP, agents and workflows) plus the final quiz. Sun: MongoDB Vector Search Fundamentals (~1h) and the RAG with MongoDB skill badge. Compare a managed vector index with your hand-rolled `Float32Array` search: what does it add, and is it worth it at your corpus size?
**Links:** learn.mongodb.com/skills
**Time:** ~2–3h Sat. MongoDB ~2.5h *(RAG badge time unverified)*.
**Deliverables:** Claude API certificate, 2 MongoDB badges, and 3–4 lines of comparison in your Project 1 notes
**Result:**

## Phase 2: Project 2 (Agent + MCP)

### 4. Intro to MCP + HF MCP Unit 1 — Not started
**Maps to:** Project 2
**Description**
Sat: Claude Academy Introduction to MCP. Sun: HF MCP course Unit 1 (architecture and core concepts) plus its quiz for the fundamentals certificate. The two overlap, so skim what repeats. Before the weekend ends, write down which tools your Project 2 server will expose.
**Links:** academy.claude.com, huggingface.co/learn/mcp-course
**Time:** ~2h Sat *(unverified)* + ~3–4h Sun
**Deliverables:** 2 certificates and a draft tool list for Project 2
**Result:**

### 5. HF Agents course Unit 1 — Not started
**Maps to:** Project 2
**Description**
The agent loop (thought → action → observation), tools, and chat templates. The certificate requires 80% on the Unit 1 quiz.
**Link:** huggingface.co/learn/agents-course
**Time:** ~3–4h *(unverified)*
**Deliverables:** Fundamentals certificate
**Result:**

## Phase 3: Project 5 (Evals and observability)

### 6. HF Agents Bonus Unit 2: Agent observability and evaluation — Not started
**Maps to:** Project 5
**Description**
Tracing agents and evaluating them. None of the 20 courses covers evals directly, and this unit is the closest match. Apply it immediately: capture a trace from your Project 2 agent.
**Time:** ~3h *(unverified)*. No certificate.
**Done when:** you have one trace of your own agent viewable in a tracing tool, with a screenshot or link in your notes.
**Result:**

## Phase 4: Projects 4 and 3 (Python required)

### 7. Kaggle Python — Not started
**Maps to:** Projects 4 and 3 (Python is required there; TypeScript stays the main language elsewhere)
**Description**
Seven short lessons with in-browser exercises. Focus on dicts, comprehensions and working with library objects. Skip what you know.
**Link:** kaggle.com/learn/python
**Time:** ~5h (Kaggle's listed time)
**Deliverables:** Kaggle certificate
**Result:**

### 8. Kaggle Intro to Deep Learning — Not started
**Maps to:** Project 3
**Description**
Neural-net basics (layers, loss, training loop) before you fine-tune.
**Link:** kaggle.com/learn/intro-to-deep-learning
**Time:** ~4h *(unverified)*
**Deliverables:** Kaggle certificate
**Result:**

---

## Optional

### 9. HF MCP course: full completion (Units 2–3) — Not started
Build and deploy an end-to-end MCP app. Do it only if the build doubles as Project 2 work. ~3–4h per unit.

### 10. HF Agents course: full completion — Not started
Requires Unit 1, one use-case assignment, and a final agent scoring ≥30% on the benchmark. Total ~20–30h. Worth it only if Project 2 needs a framework comparison (smolagents / LangGraph / LlamaIndex).

### 11. Neo4j & Generative AI certification — Not started
A 1h open-book exam: 50 questions on RAG, vector search, knowledge graphs, neo4j-graphrag and MCP tools. Pass mark 80%, free retakes after 24h. Take it only if you extend Project 1 with GraphRAG. Prep courses on GraphAcademy take ~3–4h *(unverified)*.

### 12. Databricks GenAI Fundamentals — Not started
About 1h of video plus a 15-minute quiz. Shallow, and the badge expires after 2 years. Fills a spare weekend at most.

### 13. CS50 AI — Parked
High quality, but it covers classical AI rather than LLM engineering. The free certificate needs ≥70% on every project, which takes weeks. Revisit after the core projects.

---

## Skipped from the source post
- **Google Intro to Generative AI**: a short overview, already covered by tasks 1–3.
- **Google Gen AI for Educators**: wrong audience.
- **Microsoft Applied Skills**: Azure-specific lab assessments. Take it only if a target job is Azure-heavy.
- **HF Deep RL, HF Audio, Kaggle Computer Vision**: not on the path to the seven projects.
- **Kaggle Intro to ML, Kaggle AI Ethics**: low payoff for LLM engineering.
- **freeCodeCamp ML with Python**: long and built on classical TensorFlow; poor value per hour here.
- **IBM SkillsBuild**: beginner level.
- **Salesforce Agentblazer**: vendor-specific to Salesforce.
