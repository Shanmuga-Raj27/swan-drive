# Swan Drive — Automated Testing & Quality Assurance Summary

> **Executive Overview:** This document provides a transparent, verified summary of the automated testing suites, AI retrieval benchmarks, and quality assurance standards implemented across the **Swan Drive** personal cloud document and conversational Knowledge Base platform.

---

## 1. Executive Summary & Testing Philosophy

Modern cloud applications require rigorous verification across multiple failure domains—from data isolation and cryptographic security to AI search accuracy and real-time streaming interfaces. Swan Drive employs a multi-tiered testing strategy ensuring that every layer behaves reliably, securely, and deterministically.

* **Backend Testing:** Validates data integrity, JWT authentication, tenant boundary enforcement, fail-open caching, document parsing, and zero-downtime RAG indexing.
* **AI Search Evaluation:** Measures semantic retrieval quality and precision using golden benchmark queries against vector embeddings.
* **Frontend Testing:** Guarantees predictable user experiences by validating Server-Sent Events (SSE) stream parsing, chat persistence, and strict document selection business rules.

---

## 2. Verified Test Execution Metrics

All metrics below are extracted directly from automated test runs recorded in this repository.

| Test Layer | Framework / Tool | Scope & Focus Area | Verified Result | Execution Time | Detailed Report Link |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **Backend Core & RAG** | `pytest 9.1.1` + `anyio` | Unit, Route Integration, Scenarios & Security | **417 / 417 Passed** (100%) | 7.27s | [Full Backend Report](backend-test/full_backend_test_report.md) |
| **AI Search Benchmark** | Custom Accuracy Evaluator | Semantic Vector Retrieval (Top-1 & Top-3 Rank) | **15 / 15 Passed** (100% Top-3) | Real-time run | [AI Search Evaluation Report](backend-test/AI_search_evaluation.md) |
| **Frontend Core & Logic** | `Vitest 5.1.0` | SSE Stream Parsers, Selection Rules, Storage | **39 / 39 Passed** (100%) | 603ms | [Frontend Test Report](frontend-test/frontend_test_report_1.md) |

---

## 3. Detailed Test Suite Breakdown

### A. Automated Backend Testing (Pytest)
The backend automated suite covers **417 tests** distributed across dedicated testing boundaries:

* **RAG Pipeline Unit Tests (19 Modules):** Validates 800-word/100-word sliding-window chunking, PyMuPDF text extraction, deterministic UUIDv5 generation, cosine distance matching, citation validation, and multi-model fallback streaming.
* **Route Integration Tests:** Verifies FastAPI endpoint contracts for presigned upload URLs, file pagination, metadata updates, and system metrics.
* **Multi-Tenant Security & Isolation:** Explicitly tests that User A cannot read, search, delete, or guess document IDs or vector chunks belonging to User B.
* **Fault Tolerance & Scenarios:** Asserts fail-open behavior when caching layers or vector stores experience transient outages.

📄 *Full test execution logs:* [backend-test/full_backend_test_report.md](backend-test/full_backend_test_report.md)

---

### B. AI Vector Search Accuracy Evaluation (Smoke Benchmark)
To assess information retrieval accuracy beyond binary pass/fail unit tests, an automated evaluation pipeline benchmarks semantic search precision against realistic data:

* **Evaluation Dataset:** 100 seeded documents spanning diverse domains (software engineering, finance, legal contracts, medical reports, travel itineraries, recipes).
* **Test Queries:** 15 standardized golden queries testing technical terminology, natural language phrases, and exact document intents.
* **Verified Accuracy Results:**
  * **Top-1 Exact Match Precision:** **80.0%** (12 out of 15 queries returned the target document as the first result).
  * **Top-3 Retrieval Precision:** **100.0%** (15 out of 15 queries included the target document in the top 3 positions).
* **Automated Teardown:** Dynamically provisions a dedicated test user and purges all test vectors and relational rows upon completion.

📄 *Full benchmark trace & query breakdown:* [backend-test/AI_search_evaluation.md](backend-test/AI_search_evaluation.md)

---

### C. Frontend Service & Component Logic Testing (Vitest)
The frontend testing suite isolates and validates mission-critical client logic using Vitest:

* **SSE Stream Parser (`ragApi.test.js`):** Ensures resilient streaming token assembly, comment stripping, CRLF compatibility, and extraction of cited source pills from backend events.
* **Selection Rules Engine (`ragService.test.js`):** Enforces hard domain constraints (e.g., maximum 5 selected documents for AI chat, strict exclusion of un-indexed files).
* **Session Persistence (`ragHistoryService.test.js`):** Validates localStorage round-trip integrity, per-user storage isolation, and corruption recovery.
* **Vault Partitioning (`vaultSelectionService.test.js`):** Tests batch file selection workflows and status filtering.

📄 *Full test suite results:* [frontend-test/frontend_test_report_1.md](frontend-test/frontend_test_report_1.md)

---

## 4. Testing Frameworks & Tooling Stack

| Domain | Tool / Library | Role in Testing Architecture |
| :--- | :--- | :--- |
| **Backend Test Runner** | `pytest` (v9.1.1) | Primary test runner and assertion framework |
| **Async Test Runtime** | `anyio` (v4.14.2) | Manages asynchronous FastAPI endpoint test execution |
| **API Client Mocking** | `unittest.mock` / `AsyncMock` | Simulates S3 object storage, Gemini LLM streams, and Redis cache |
| **Database Isolation** | `SQLite (StaticPool)` / MySQL | In-memory and transient test database environments |
| **Frontend Test Runner** | `Vitest` (v5.1.0) | High-performance ESM-native test runner |
| **Vector Engine Evaluation** | Qdrant Cloud Client | Live cosine distance ranking and payload isolation evaluation |

---

## 5. How to Run the Tests

Contributors can reproduce and execute all test suites locally using the verified commands below.

### Running Backend Tests
Navigate to the `backend/` directory:

```bash
cd backend

# Run the complete automated backend test suite (417 tests):
pytest

# Run tests with verbose output:
pytest -v

# Run a specific test subsystem (e.g., RAG unit tests):
pytest tests/unit/services/rag/

# Run multi-tenant security and isolation tests:
pytest tests/integration/security/
```

### Running Frontend Tests
Navigate to the `frontend/` directory:

```bash
cd frontend

# Run all frontend unit tests once:
npm test
# or
npx vitest run

# Run in interactive watch mode during development:
npx vitest
```

### Running AI Search Evaluation & Infrastructure Smoke Tests
From the `backend/` directory (requires active `.env` credentials):

```bash
cd backend

# Run AI Search Accuracy evaluation against 100 seeded documents:
uv run python tests/AI-test/evaluate_search.py

# Run Phase 2 PDF extraction and chunking smoke test:
uv run python -m app.scripts.rag_phase2_smoke uploads/<sample-document>.pdf

# Run Phase 5 end-to-end RAG infrastructure smoke test:
uv run python -m app.scripts.rag_phase5_smoke <user_id> uploads/<s3_key> "<sample question>"
```

---

## 6. Execution Environment & Test Limitations

To maintain full transparency, the following technical boundaries should be noted:

* **Execution Platform:** Verified on Linux (`x86_64`), Python 3.12.15, and Node.js with Vite 8 / React 19.
* **Mocked vs. Live Cloud Infrastructure:**
  * Automated backend unit/integration tests use in-memory SQLite and mock fixtures for high execution speed (7.27 seconds) and zero external API dependencies.
  * Smoke tests and AI evaluation scripts (`rag_phase5_smoke.py`, `evaluate_search.py`) connect to live cloud services (AWS RDS, Qdrant Cloud, Upstash Redis, Google Gemini) and require network access and valid credentials in `others/.env`.
* **End-to-End Browser Testing:** Comprehensive browser-level E2E UI automation (e.g., Cypress or Playwright) is currently not implemented; frontend UI stability is validated via Vitest logic tests and Vite production build validation (`npm run build`).
* **Code Coverage:** Total codebase line/branch coverage percentages are not tracked in the current test configuration.

---

## 7. Conclusion

Swan Drive's automated testing architecture demonstrates enterprise-grade engineering discipline: **456 combined automated tests across frontend and backend execute in under 8 seconds**, complemented by an objective **AI retrieval accuracy benchmark achieving 100% Top-3 precision**. This automated safety net guarantees that new features, security updates, and performance optimizations can be delivered with high confidence.
