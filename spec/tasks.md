# Tasks: Insurance Plan RAG Query Engine (v1)

**How we work:** one task at a time. I write the code, you run the check in "Verify", and we commit only after it passes. Tick `[x]` once a task is committed.

## Phase A: Foundations
| ✓ | # | Task | Refs | Verify |
|---|---|---|---|---|
| [ ] | T1 | Scaffold: folders, `requirements.txt`, `.gitignore`, `.env.example`, `config.py`, `/health` | NFR5 | `uvicorn` starts; the `/docs` page opens in the browser |
| [ ] | T2 | Model client per role (OpenAI-compatible), with retry and JSON validation | NFR6 | A smoke script gets a reply from both roles on Groq |
| [ ] | T3 | SQLite database: plans, versions, audit log tables | R4.2, NFR4 | Tests: create a plan, add a version, switch the current version |

## Phase B: Ingestion
| ✓ | # | Task | Refs | Verify |
|---|---|---|---|---|
| [ ] | T4 | PDF extraction: paragraphs, headings, table rows, header/footer removal, scanned-PDF check | R10.2 | On 1 health + 1 life brochure, the printed paragraphs look clean to you |
| [ ] | T5 | Version info extraction (UIN, date, version code) | R4.3 | The values found match what's printed on your brochures |
| [ ] | T6 | Labelling: batched, with context; section, tags, and example flag | R10.3 | ≥ 85% agreement with your hand labels |
| [ ] | T7 | Example dropping and section-bounded chunking | R10.3 | Tests: no chunk spans two sections; dropped examples are listed |
| [ ] | T8 | Embedder, ChromaDB store, and version replacement | R4.2 | Test: after re-uploading a changed brochure, only the new version is searchable |
| [ ] | T9 | Upload pipeline and admin endpoints (upload, list, delete), with the upload report | R10.1 | Upload a brochure via `/docs`; the report shows sensible section counts |
| [ ] | T10 | Catalogue endpoints, page images, and the file endpoint | R9, R11 | The selector lists and a page image work via `/docs` |

## Phase C: Answering
| ✓ | # | Task | Refs | Verify |
|---|---|---|---|---|
| [ ] | T11 | Plan resolution (IDs or fuzzy name matching, with clarification) and question-type detection | R2, R1.5, R1.6 | Tests with exact, misspelt, ambiguous, and not-uploaded plan names |
| [ ] | T12 | Guardrail checks and templates | R3.11, R6 | Unit tests catch each prohibited pattern |
| [ ] | T13 | Single-plan and definition flow, plus the miss flow | R1.1, R3, R7, R8 | 10 questions via `/docs`: answers are cited; misses show the page and 2 passages |
| [ ] | T14 | Cross-plan flow | R1.2, R5.1, R6.1 | Scope is stated; lists are alphabetical; plans where nothing was found have brochure links |
| [ ] | T15 | Comparison flow (side-by-side and differences), with the comparison-rows admin | R1.3, R1.4, R3.4, R3.9, R10.4 | A 3-plan comparison has quote + page cells and verified "not stated" cells |
| [ ] | T16 | Audit logging with `request_id` and the models used | NFR4 | Every `/ask` call appears in the log with its sources and versions |

## Phase D: Quality and handoff
| ✓ | # | Task | Refs | Verify |
|---|---|---|---|---|
| [ ] | T17 | Eval harness and golden-set template | §6 | `run_eval.py` prints the metrics table |
| [ ] | T18 | Model swap run: evaluate the QA-env models via config only | NFR6, O4 | The eval runs with only `.env` changed; the results table compares models |
| [ ] | T19 | Dockerfile and API handoff notes | NFR7 | `docker run` serves the API; the notes explain every endpoint |

## Phase E: Expo prototype app
| ✓ | # | Task | Refs | Verify |
|---|---|---|---|---|
| [ ] | M1 | Expo setup, connected to the engine's public Codespaces URL | NFR5 | The app on your phone shows the `/health` result |
| [ ] | M2 | Ask screen: free text plus the product → insurer → plan selector | R2, R9 | Picking a plan and asking returns a response |
| [ ] | M3 | Answer card: lead, detail, citations, sources, "Answering for" | R2.5, R4.4, R8 | Matches the response shape in design §3.7 |
| [ ] | M4 | Comparison table | R1.4 | 3 plans are readable side by side on a phone |
| [ ] | M5 | Not-found card and brochure page viewer | R3.8, R11 | The page image and related passages display |

## Your inputs, and when they're needed
| Before | Input |
|---|---|
| T2 | A free Groq API key from console.groq.com |
| T4 | 2 health and 2 life brochure PDFs uploaded to `data/pdfs/` |
| T6 | About 40 hand-labelled paragraphs (a template ships with T4) |
| T15 | The default comparison rows for health and life (O1) |
| T17 | Golden questions per plan (a template ships with T17) |
| T18 | The QA-env API format, and network access from Codespaces (O4) |