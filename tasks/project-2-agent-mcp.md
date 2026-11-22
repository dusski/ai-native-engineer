# Project 2: Agent + MCP Server — Task Plan

**How to use this file**
- Do one task at a time, top to bottom. Each task should take 1–3 hours.
- A task is Done only when its "Done when" check passes. Record the evidence (numbers, file paths, trace links) in the task's Result line.
- To update this file, tell Claude, for example: "Task 6 done, trace link …". Task state lives only here: tick the TOC box and fill the Result line. Items to raise at a later task go in that task's `Open:` line and are deleted when resolved. `progress.md` changes only when a checkpoint or the project Status changes.

**Stack**
- Language: TypeScript on Node, same tooling as P1 (`tsx`, `vitest`). Matches the TypeScript-end-to-end target posting.
- MCP: the official TypeScript SDK. Target the latest spec revision (2026-07-28 per the resource map, section 4) and check which SDK package and version support it before you start; some tutorials predate that revision.
- Model: Claude API with tool use. A second provider joins in Phase D, behind the gateway.
- Validation: Zod for tool inputs and model output.
- Tracing: Langfuse (also P5's tool; set up once here, reused there).
- No agent framework (LangGraph and the like). Start with a plain tool-calling loop and add structure only when the task needs it (Anthropic, "Building effective agents", resource map section 2).

**Where the checkpoints land:** 1 → task 4 · 2 → task 6 · 3 → task 7 · 4 (gateway-lite) → task 10 · 5 (attack your own agent) → task 13 · 6 (write-up) → task 14. Background for 4 and 5: `references/guides-extract.md`, sections 5.1 and 5.2.

---

## TOC

**Phase A: Design and setup**
1. [ ] Choose the agent's job and its tools
2. [ ] Set up the repo and environment

**Phase B: The MCP server**
3. [ ] First tool, tested with MCP Inspector
4. [ ] Second tool and structured tool status

**Phase C: The agent**
5. [ ] Plain tool-calling loop over the MCP server
6. [ ] A real multi-step task, traced
7. [ ] Budget ceiling and failure recovery

**Phase D: Gateway-lite**
8. [ ] `callModel()` with two providers and required metadata
9. [ ] Schema validation, repair, retries by error type, fallback
10. [ ] Circuit breaker, chaos switch, cost per call

**Phase E: Attack your own agent**
11. [ ] Attack set and benign set, baseline run
12. [ ] Layered defences
13. [ ] Re-run, report, close one bypass

**Phase F: Wrap-up**
14. [ ] Write-up README with design choices and numbers

---

## Phase A: Design and setup

### 1. Choose the agent's job and its tools
**Description**
Pick one real multi-step job you'd actually use, which needs 2–4 tools to finish. At least one tool should be irreversible (writes a file, sends a message, creates an issue), because Phase E needs one to protect. At least one tool should read untrusted text (web page, document, email, issue body), because that is where injection comes from. One option: reuse the P1 corpus, with a `search_docs` tool calling the P1 retriever plus a write tool. For each tool, write its name, input schema, output, and whether it is reversible.
**Learn:** Anthropic, "Writing tools for agents" (resource map, section 2).
**Deliverables**
- `DESIGN.md`: the job in two sentences, the tool table, and one example run written out by hand (which tools, in which order)
**Done when:** you can name the irreversible tool and the untrusted-input tool, and the example run needs at least 3 tool calls.
**Time:** ~1–2h
**Result:**

### 2. Set up the repo and environment
**Description**
Create `projects/agent-mcp` as its own git repo (link it in `progress.md`). TypeScript project with the MCP SDK, `@anthropic-ai/sdk`, `zod`, `vitest`. API keys in `.env`, ignored by git. Note the MCP spec revision and SDK version you target in the README.
**Deliverables**
- Repo with `package.json`, `tsconfig.json`, `.gitignore`, `.env.example`
- `scripts/smoke-test.ts` that makes one Claude call
**Done when:** the smoke test prints a Claude reply and the README states the targeted MCP spec revision and SDK version.
**Time:** ~1h
**Result:**

## Phase B: The MCP server

### 3. First tool, tested with MCP Inspector
**Description**
Write the MCP server with one tool from task 1, its input schema in Zod. Run it over stdio and call the tool from MCP Inspector (the official debugging UI; check the current way to launch it in the MCP docs).
**Learn:** the MCP lifecycle: initialise, list tools, call tool. Which parts the spec revision you target changed.
**Deliverables**
- `src/server.ts` with one tool
- A screenshot or pasted output of the Inspector call in `notes.md`
**Done when:** Inspector lists the tool and a call returns the expected result.
**Time:** ~2h
**Result:**

### 4. Second tool and structured tool status
**Description**
Add the remaining tools. Every tool returns a structured result with a `status` of `ok`, `empty`, `rate_limited`, `timeout` or `error`, plus data or a short message, instead of throwing. The model can reason about "no results" or "try later"; it can't reason about a stack trace. Give each tool a timeout.
**Deliverables**
- All tools from task 1 in `src/server.ts`
- Unit tests: each tool returns `empty` and `timeout` correctly (fake the slow or empty case)
**Done when:** Inspector lists 2+ tools and the tests pass. **Checkpoint 1 done.**
**Time:** ~2–3h
**Result:**

## Phase C: The agent

### 5. Plain tool-calling loop over the MCP server
**Description**
Write `src/agent.ts`: an MCP client that connects to your server, lists its tools, passes them to Claude as tools, and loops: send messages → if the model asks for a tool, call it over MCP and append the result → repeat until the model stops. Log each step (tool name, input, status, duration).
**Learn:** the agent loop (thought → action → observation). The HF Agents course Unit 1 (weekend certs, task 5) covers the same loop.
**Deliverables**
- `src/agent.ts` (CLI: `npx tsx src/agent.ts "task"`)
**Done when:** the agent finishes a simple one-tool task end to end.
**Time:** ~2–3h
**Result:**

### 6. A real multi-step task, traced
**Description**
Run the example task from `DESIGN.md`. Add Langfuse tracing: one trace per run, one span per model call and per tool call, with tokens and latency. Cloud or self-hosted is your choice; check the current free tier and SDK for TypeScript before choosing.
**Deliverables**
- A Langfuse trace of a successful run, linked or screenshotted in `notes.md`
- Three runs of the task: how many succeeded, and what went wrong in the others
**Done when:** a trace shows 3+ tool calls completing the real task. **Checkpoint 2 done.** (This also gives P5 its P2 traces.)
**Time:** ~2h
**Result:**

### 7. Budget ceiling and failure recovery
**Description**
Enforce a budget in code: max steps, max tokens, max dollars, max wall-clock time. When any limit is hit, the run stops with a clear reason instead of looping. Then make a tool fail (return `timeout` or `error`) and check the agent retries or works around it. If the agent has a self-review or retry loop, cap it at one send-back.
**Deliverables**
- `src/budget.ts`
- Tests: (a) a run stops at the step ceiling with the right reason; (b) a run recovers from one tool failure
**Done when:** both tests pass. **Checkpoint 3 done.**
**Time:** ~2–3h
**Result:**

## Phase D: Gateway-lite

### 8. `callModel()` with two providers and required metadata
**Description**
Write `src/gateway/` with one function, `callModel(request)`, which becomes the only way the agent talks to a model. Put two providers behind one interface: Claude plus one other (any provider with an API key you have; a multi-model router such as OpenRouter is one option, so check its pricing). Every call requires `requestId`, `feature` and `tenant`; refuse calls without them. Move the agent onto `callModel()`.
**Learn:** why metadata is required: without it you can't attribute cost or debug a request later.
**Deliverables**
- `src/gateway/index.ts`, one adapter per provider
- The agent runs on either provider by config
**Done when:** the task from task 6 passes on both providers, and a call without metadata is rejected by a test.
**Time:** ~2–3h
**Result:**

### 9. Schema validation, repair, retries by error type, fallback
**Description**
When `callModel()` expects structured output, validate it with a Zod schema. If invalid: send one repair request with the validation error; if still invalid, fall back to the second provider. Classify errors: rate limit, timeout, server error, content filter, auth failure. Retry only the retryable ones (rate limit, timeout, server error) with backoff and jitter; never retry auth failures. After retries run out, fall back.
**Deliverables**
- `src/gateway/errors.ts` (taxonomy) and the repair and fallback logic
- Tests with fake providers: invalid → repaired; invalid twice → fallback; auth error → no retry; rate limit → retried then fallback
**Done when:** all four tests pass.
**Time:** ~2–3h
**Result:**

### 10. Circuit breaker, chaos switch, cost per call
**Description**
Add a circuit breaker per provider: closed (normal), open (skip this provider after N failures), half-open (after a cooldown, let a small share of calls through to test recovery). Add a chaos switch (env flag) that makes one provider fail or slow down. Compute cost per call from tokens × price (keep prices in one config file, with the date you checked them) and attach it to the Langfuse span.
**Deliverables**
- `src/gateway/breaker.ts`, chaos flag documented in the README
- A trace of a run with chaos on: failures, breaker opening, fallback, and recovery through half-open
**Done when:** the chaos trace shows fallback and all three breaker states, and every model span shows its cost. **Checkpoint 4 done.**
**Time:** ~3h
**Result:**

## Phase E: Attack your own agent

### 11. Attack set and benign set, baseline run
**Description**
Write 20–30 attacks as rows: `id, family, payload, where it enters, expected safe behaviour`. Families: direct instruction override; injection hidden in a document or tool result; call to a tool outside the allowed set; data exfiltration through output (for example a URL carrying data); encoding tricks (base64, unusual unicode); system-prompt extraction. Most attacks should arrive through the untrusted-input tool, not the user prompt. Write ~30 benign tasks too. Run everything against the current agent with the irreversible tool pointed at a sandbox, and record which attacks succeed. Only attack your own system.
**Learn:** OWASP Top 10 for LLM Applications (resource map, section 12.6). Optional: garak (generic model probes) and PyRIT (multi-turn), both Python; they don't know your tools, so your own rows matter more.
**Deliverables**
- `attacks/attacks.jsonl`, `attacks/benign.jsonl`
- `attacks/run.ts` that prints success rate by family
**Done when:** a baseline attack success rate by family is recorded, before any defence.
**Time:** ~2–3h
**Result:**

### 12. Layered defences
**Description**
Add defences in layers, each logging the rule that fired: (1) untrusted content wrapped and labelled as data, never as instructions; (2) a per-context tool allowlist (a run reading untrusted content can't call the irreversible tool without approval); (3) a required human approval step before any irreversible action; (4) an output scan for secrets and outbound URLs carrying data. Filtering the phrase "ignore previous instructions" is theatre, and a system prompt asking the model nicely is not a control. The real control is limiting what the agent can do once it is fooled.
**Deliverables**
- `src/defence/` with one module per layer
- Every block writes `{attackId?, rule, input excerpt}` to the trace
**Done when:** each layer has a unit test, and a block from each layer shows up in a trace.
**Time:** ~3h
**Result:**

### 13. Re-run, report, close one bypass
**Description**
Re-run attacks and benign tasks. Report attack success by family before and after, and the false-positive rate on benign tasks (benign work blocked or sent for approval). Then try to bypass your own defences until one works; fix it and add it as a permanent attack row.
**Deliverables**
- `attacks/REPORT.md`: before/after table by family, false-positive rate, the bypass written up (what worked, why, the fix)
**Done when:** the report exists with all three numbers and the bypass row passes after the fix. **Checkpoint 5 done.**
**Time:** ~2h
**Result:**

## Phase F: Wrap-up

### 14. Write-up README with design choices and numbers
**Description**
Lead with results: task success rate over N runs, cost per run, the chaos trace, attack success before/after, false-positive rate. Then the design choices and why: plain loop over a framework, structured tool status, budget, gateway shape, defence layers. Then what failed and what you'd change, and how to run it.
**Deliverables**
- `README.md`
- Repo link in `progress.md`
**Done when:** a stranger can clone the repo, run the agent and the attack suite by following the README. **Checkpoint 6 done.**
**Time:** ~2h
**Result:**
