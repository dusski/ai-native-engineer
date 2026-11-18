# Project 1: Production RAG — Task Plan

**How to use this file**
- Do one task at a time, top to bottom. Each task should take 1–3 hours.
- A task is Done only when its "Done when" check passes. Record the evidence (numbers, file paths) in the task's Result line.
- Status values: Not started / In progress / Done / Skipped.
- To update this file, tell Claude, for example: "Task 7 done, baseline Pass@10 = 0.78". One milestone updates three places together: the Result line here, `progress.md` (checkpoint + Status), and its log.

**Stack (chosen for running locally, with minimal accounts)**
- Language: TypeScript on Node (decision 2026-11-17: learn RAG concepts without learning Python at the same time). Python returns in projects 3 and 4, where it is required. The earlier Python scaffold (`uv`, `scripts/smoke_test.py`) is superseded.
- Embeddings and reranker: local ONNX models via `@huggingface/transformers` (transformers.js). They run on CPU, need no API key, and cost nothing. Fallback if the task 1 spike fails: a hosted embedding/rerank API (small cost, extra account). Model availability and versions: unverified, check in the spike.
- Keyword search: SQLite FTS5 via `better-sqlite3`, or a small npm BM25 library (choose in task 9).
- Answer generation: Claude API (needs `ANTHROPIC_API_KEY`; cost should stay well under $5).
- No framework (LangChain, LlamaIndex), no vector DB, no UI. You write each piece yourself so you understand it.

---

## TOC

**Phase A: Setup and data**
1. [x] Set up the repo and environment
2. [ ] Choose and collect the document set
3. [ ] Load and chunk the documents

**Phase B: Measuring before building**
4. [ ] Write the hand-made eval set (20 questions)
5. [ ] Expand the eval set to 40+ questions

**Phase C: Baseline RAG**
6. [ ] Embed all chunks and cache them
7. [ ] Vector search + Pass@k eval script → baseline number
8. [ ] Generate answers end to end

**Phase D: Better retrieval**
9. [ ] BM25 keyword search on its own
10. [ ] Hybrid search with RRF fusion
11. [ ] Add a reranker

**Phase E: Trustworthy answers**
12. [ ] Citations, validated in code
13. [ ] Error analysis: read the failures

**Phase F: Optional and wrap-up**
14. [ ] (Optional) Contextual retrieval ablation
15. [ ] Write-up README with the results table
16. [ ] (Optional) Deploy a live demo

---

## Phase A: Setup and data

### 1. Set up the repo and environment — Done
**Description**
Create a git repo for this project (its own repo inside `projects/`, which this workspace ignores, for example `projects/rag-hybrid-search`; link it in `progress.md`), and create a TypeScript project (Node, `tsx`, `vitest`). Add `@anthropic-ai/sdk` and `@huggingface/transformers`. Put the API key in a `.env` that is excluded by `.gitignore`. Make one test call to Claude, one test embedding, and one test rerank of a few query/chunk pairs (the spike: if the rerank doesn't work locally, decide on the hosted fallback now, not in task 11).
**Learn:** how transformers.js loads and caches ONNX models.
**Deliverables**
- Repo with `package.json`, `tsconfig.json`, `.gitignore`, `.env.example`
- `scripts/smoke-test.ts`
**Done when:** `npx tsx scripts/smoke-test.ts` prints a short Claude reply, an embedding vector length, and reranker scores for a few pairs.
**Time:** ~1h
**Result:** 2026-11-16: repo + uv project + deps committed. `scripts/smoke_test.py` Claude half works (reply printed, stop_reason `end_turn`; first run failed with a 400 "credit balance too low", second run passed). Python scaffold superseded by the TypeScript decision (2026-11-17); 2026-11-17: repo re-initialised as TypeScript on `main` (Python kept on branch `python-version`). `scripts/smoke-test.ts` Claude half works (typecheck clean, reply printed, stop_reason `end_turn`). 2026-11-18: Done. Embedding: `onnx-community/all-MiniLM-L6-v2-ONNX`, length 384, sum of squares 1.0000001 (normalized), cached load ~130–200 ms, one embed ~10–15 ms. Rerank spike passed locally, so no hosted fallback: `Xenova/bge-reranker-base` with `dtype: "q8"` (`model_quantized.onnx`, 266 MB; without it Node defaults to fp32, the 1.1 GB file). The `text-classification` pipeline can't take pairs and would softmax a single logit to 1.0, so it uses `AutoTokenizer` (`text_pair`) + `AutoModelForSequenceClassification` with raw logits. Cached load ~690–765 ms; 3 pairs (186 tokens padded) score in ~124–141 ms. Logits: relevant 7.18, plausible-but-wrong 0.25, near-topic non-answer 0.39 (the two non-answers score about equal and far below the relevant one). One run gave 6.88 / -0.26 / -10.18; cause not confirmed.

### 2. Choose and collect the document set — Not started
**Description**
Choose 50–200 documents you know well, such as the docs of a framework you use daily. Markdown sources from a GitHub repo are best because they are already clean text. Save them under `data/raw/`. Knowing the content well lets you judge answers instantly.
**Deliverables**
- `data/raw/` with the documents
- One paragraph in the README: what the corpus is, how many documents, and why you chose it
**Done when:** the files are in the repo and you can name 5 questions you'd ask about them.
**Time:** ~1h
**Result:**

### 3. Load and chunk the documents — Not started
**Description**
Write `chunk.ts`. Split each document into chunks of about 300–500 tokens with a small overlap. Prefer splitting on markdown headings or paragraphs over fixed character counts. Give each chunk a stable `id`, its `source` file and its `heading`. Save everything to `data/chunks.jsonl`.
**Learn:** why chunk size matters. Chunks that are too small lose context, and chunks that are too big dilute the match.
**Deliverables**
- `chunk.ts`
- `data/chunks.jsonl`
**Done when:** you open 5 random chunks and each one reads as a self-contained piece of text. Record the chunk count.
**Time:** ~2h
**Result:**

## Phase B: Measuring before building

### 4. Write the hand-made eval set (20 questions) — Not started
**Description**
Write 20 realistic questions a user would ask. For each, record the chunk id(s) that contain the answer and a one-line expected answer. Mix easy lookups, questions phrased differently from the docs, and exact-term questions (names, error codes, flags).
**Learn:** Hamel Husain, "Your AI Product Needs Evals" (link in the resource map, section 7).
**Deliverables**
- `data/eval.json` as a list of entries shaped like `{question, gold_ids, expected_answer}`
**Done when:** 20 entries, and every `gold_id` exists in `chunks.jsonl`. Write a tiny check script for this.
**Time:** ~2h
**Result:**

### 5. Expand the eval set to 40+ questions — Not started
**Description**
Add 20 or more questions. You can have Claude generate candidate questions from random chunks, but review and rewrite each one. Generated questions tend to copy the chunk's wording, which makes retrieval look better than it is.
**Deliverables**
- `data/eval.json` with 40+ entries, each tagged `"origin": "hand"` or `"origin": "generated"`
**Done when:** 40+ entries that you've reviewed, and the validity check from task 4 still passes.
**Time:** ~1–2h
**Result:**

## Phase C: Baseline RAG

### 6. Embed all chunks and cache them — Not started
**Description**
Embed every chunk with a small local embedding model and save the vectors to disk, so you embed once and reuse. Pick a model from the MTEB leaderboard that has an ONNX build usable in transformers.js, and check its model card for the max input length.
**Learn:** what an embedding is, and why cosine similarity is used to compare them.
**Deliverables**
- `embed.ts`
- `data/embeddings.bin` (vectors as raw `Float32Array`) plus ids in the same order
**Done when:** the vector count equals the chunk count, and a second run loads from cache instantly.
**Time:** ~1–2h
**Result:**

### 7. Vector search + Pass@k eval script → baseline number — Not started
**Description**
Write `retrieve_vector(query, k)`, which embeds the query and returns the top-k chunk ids by cosine similarity (dot product over `Float32Array`s of normalized vectors). Write `eval-retrieval.ts`, which runs every eval question and prints Pass@5 and Pass@10. Pass@k means "was a gold chunk in the top k?"
**Deliverables**
- `retrieve.ts` with `retrieve_vector`
- `eval-retrieval.ts` that prints a results line
- `results.md` table with its first row: `vector | Pass@5 | Pass@10`
**Done when:** the baseline numbers are recorded. **This is the most important number in the project.** Every later task is compared against it.
**Time:** ~2h
**Result:**

### 8. Generate answers end to end — Not started
**Description**
Write `ask.ts "question"`. It retrieves the top 5 chunks, puts them in a prompt, and asks Claude to answer *only* from them, or to say "I don't know". Use a current small model; check the Claude models page for the ID.
**Deliverables**
- `ask.ts` (CLI)
- 3 example question/answer outputs pasted into `results.md`
**Done when:** you get good answers to known questions, and "I don't know" for a question the docs don't cover.
**Time:** ~1–2h
**Result:**

## Phase D: Better retrieval

### 9. BM25 keyword search on its own — Not started
**Description**
Add `retrieve_bm25(query, k)` using a BM25 library or SQLite FTS5. Run the same eval. Compare which questions vector search gets right and BM25 misses, and the reverse.
**Learn:** what BM25 scores and why it wins on exact terms (names, codes) where embeddings struggle.
**Deliverables**
- `retrieve_bm25` in `retrieve.ts`
- New row in `results.md`
**Done when:** BM25 Pass@k is recorded, and you can name 2 questions where BM25 beats vector search (or say that none exist).
**Time:** ~1–2h
**Result:**

### 10. Hybrid search with RRF fusion — Not started
**Description**
Add `retrieve_hybrid`. Take the top 50 from each retriever and merge them with Reciprocal Rank Fusion. Use `k=60`, the standard default; don't tune it yet.
**Deliverables**
- `rrf()` and `retrieve_hybrid` in `retrieve.ts`
- New row in `results.md`
**Done when:** the hybrid Pass@k is recorded next to vector and BM25.
**Time:** ~1h
**Result:**

### 11. Add a reranker — Not started
**Description**
Pass hybrid's top ~50 through a local cross-encoder reranker (transformers.js, or the hosted fallback chosen in task 1) and keep the top 10. Unlike embeddings, a cross-encoder reads the query and the chunk together, so it is slower but more accurate. Measure the time per query.
**Deliverables**
- `retrieve_reranked` in `retrieve.ts`
- New row in `results.md`, with an added latency column (ms/query)
**Done when:** the reranked Pass@k and latency are recorded. Write down whether the accuracy gain is worth the latency.
**Time:** ~2h
**Result:**

## Phase E: Trustworthy answers

### 12. Citations, validated in code — Not started
**Description**
Switch `ask.ts` to the best retriever. Label chunks `[1]…[n]` in the prompt and require a citation after every claim. After the answer comes back, parse the cited numbers in code, check that each one was actually provided, and print the source file next to it. Then run all eval questions and count valid citations.
**Deliverables**
- Updated `ask.ts` that prints the answer with sources
- `% answers with valid citations` in `results.md`
**Done when:** an example cited answer is in `results.md`, and the invalid-citation rate is measured.
**Time:** ~2–3h
**Result:**

### 13. Error analysis: read the failures — Not started
**Description**
List every eval question the best retriever still misses. Read each one and label the cause: bad chunking, vocabulary mismatch, bad gold label, or question too vague. Fix the most common cause and re-run.
**Learn:** this is the core eval skill. Looking at the data beats tuning blindly.
**Deliverables**
- A "Failure analysis" section in `results.md`: causes with counts, the fix you made, and the before/after numbers
**Done when:** one fix is applied and its effect is measured, even if the effect is zero.
**Time:** ~2h
**Result:**

## Phase F: Optional and wrap-up

### 14. (Optional) Contextual retrieval ablation — Not started
**Description**
For each chunk, have Claude write 1–2 sentences that place the chunk within its document. Prepend them to the chunk, then re-embed and re-index. Use prompt caching so the full document isn't paid for on every call. Compare against task 11.
**Learn:** Anthropic Contextual Retrieval post and cookbook (resource map, section 3).
**Deliverables**
- `contextualize.ts`
- New rows in `results.md`, plus the one-time cost in dollars
**Done when:** it is measured on your data, with a verdict on whether it was worth the cost.
**Time:** ~2–3h
**Result:**

### 15. Write-up README with the results table — Not started
**Description**
Write the README a hiring manager would read: what the project does, the architecture in 5 lines, the results table (vector → BM25 → hybrid → rerank, with latency), what failed and what you learned, and how to run it.
**Deliverables**
- `README.md`
- Repo link added to `progress.md`
**Done when:** a stranger could clone the repo and run `ask.ts` by following the README.
**Time:** ~1–2h
**Result:**

### 16. (Optional) Deploy a live demo — Not started
**Description**
The source post stresses deploying projects, not just running them locally. Make `ask.ts` reachable without cloning the repo: a small hosted endpoint, or a hosted page that takes a question and shows the cited answer. Keep it minimal and protect your API key (rate limit, spend cap). If hosting costs or key exposure outweigh the value, a recorded terminal session in the README is an acceptable substitute.
**Deliverables**
- A demo link (or recording) in the README
- One line on hosting cost and how the key is protected
**Done when:** someone else can ask a question through the link and get a cited answer.
**Time:** ~2-3h
**Result:**
