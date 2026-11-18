# Swan Drive (Personal Knowledge Base) — Technical Memory & Architecture

> Authoritative technical reference and system memory for AI agents working in this repository.
> Built directly from live source inspection. Do not rely on outdated external documentation or READMEs.

---

## 1. Project Overview & Current Tech Stack

Swan Drive is a cloud-native personal document workspace and conversational Knowledge Base (RAG). It provides secure multi-tenant file storage, metadata indexing, semantic search, and document-grounded question answering via streaming Server-Sent Events (SSE).

| Layer | Technology | Exact Version / Spec | Key Role / Details |
| :--- | :--- | :--- | :--- |
| **Backend Framework** | FastAPI | `fastapi>=0.141.1` (Python `>=3.12`) | Async REST API, Pydantic validation (`pydantic>=2.13.4`, `pydantic-settings>=2.15.0`), SSE streaming (`/rag/query`) |
| **ASGI / WSGI Server** | Uvicorn + Gunicorn | `uvicorn>=0.52.4`, `gunicorn>=26.2.0` | `gunicorn -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000 main:app` |
| **Relational Database** | AWS RDS MySQL 8.0 | `PyMySQL>=1.2.0`, `SQLAlchemy>=2.0.52` | User auth, file metadata, RAG chunk staging, corpus revisions. Pool size: 20, max overflow: 40 |
| **Database Migrations** | Alembic | `alembic>=1.19.1` | Automated versioned schema migrations (`backend/alembic/versions`) |
| **Auth & Cryptography** | Argon2id + JWT | `pwdlib[argon2]>=0.3.1`, `argon2-cffi>=25.1.0`, `python-jose>=3.5.0` | Argon2id password hashing, JWT bearer tokens (HS256, 30-min expiry) |
| **Email Service (OTP)** | Resend REST API | `httpx>=0.28.1` | Transactional email delivery for 6-digit verification OTP codes during registration and resend |
| **Vector Database** | Qdrant Cloud | `qdrant-client>=1.19.0` | Collections: `document_vault` (file metadata) & `document_chunks_v1` (RAG chunks), 768d Cosine |
| **Embeddings** | Google Gemini API | `google-genai>=2.20.0` | Model: `gemini-embedding-2` (768 dimensions, Cosine distance) |
| **LLM Generation** | Google Gemini API | `google-genai>=2.20.0` (Client) | Multi-model fallback list: `gemini-3.1-flash-lite`, `gemini-3.5-flash-lite`, `gemini-2.5-flash-lite`, `gemini-3-flash`, `gemma-4-26b-a4b-it` |
| **Cache Layer** | Upstash Redis (REST) | `httpx>=0.28.1`, `upstash-redis>=1.9.0` | Fail-open RAG answer caching (TTL: 86400s / 24h), rate limiting |
| **Object Storage** | AWS S3 / Backblaze B2 | `boto3>=1.43.82`, `botocore>=1.43.82` | Direct client PUT/GET presigned URLs (expiry: 3600s upload, 300s view) |
| **Document Processing**| PyMuPDF + Custom Extr. | `pymupdf>=1.28.2` | Multi-format parsers: PDF, DOCX, XLSX, CSV, TSV, MD, TXT, LOG, RTF (50MB max file size) |
| **Inspection & Admin** | Streamlit | `streamlit>=1.63.0` | Developer / evaluation playground (`backend/streamlit_app/app.py`) |
| **Backend Testing** | Pytest | `pytest>=9.1.1` | Unit, route integration, scenario, security, and AI retrieval test suites |
| **Frontend UI** | React 19 + Vite | `react==^19.2.8`, `react-dom==^19.2.8`, `vite==^8.2.2` | Single Page Application (SPA), dynamic tab view switching, SEO title management |
| **Component Library** | MUI Material UI 9 | `@mui/material==^9.3.1`, `@mui/icons-material==^9.3.1`, `@emotion/react==^11.14.0`, `@emotion/styled==^11.14.1` | Strictly MUI `sx` prop + design tokens (`theme.js`), zero CSS/SCSS files |
| **Markdown & Citations**| React Markdown | `react-markdown==^10.1.0`, `remark-gfm==^4.0.1` | Markdown response rendering with interactive source citation pills |
| **Frontend HTTP** | Axios | `axios==^1.19.0` | Centralized Axios client, JWT authorization interceptor, 401 token eviction, manual SSE streaming parser |
| **Frontend Analytics** | Vercel Analytics | `@vercel/analytics==^2.0.1` | Telemetry and web vitals tracking |
| **Frontend Testing** | Vitest | `vitest==^5.0.0` | Unit tests for API parsers, RAG selection rules, and history persistence |
| **Frontend Hosting** | Vercel Edge CDN | Serverless SPA hosting | Edge proxy rewrite: `/backend/:path*` -> `https://api.swandrive.me/:path*` |
| **Backend Compute** | AWS EC2 `t3.micro` | Ubuntu Linux + Docker Compose | Single `pkb-backend` container on port 8000 behind host Nginx reverse proxy |

---

## 2. Directory Structure & File Lookup Map

```text
Personal_Knowledge_Base/
├── .agents/                               # Agent memory, rules & implementation plans
│   ├── Implementation plan/               # Feature roadmaps and technical specs
│   │   ├── account_deletion_implementation_plan.md
│   │   └── auth_OTP_implementation_plan.md
│   ├── agent-memory.md                    # Comprehensive architectural memory & file lookup
│   └── agent-rules.md                     # Mandatory execution rules and constraints for AI agents
├── backend/                               # FastAPI backend application
│   ├── alembic/                           # Database migration management
│   │   ├── versions/                      # Migration revisions (metadata, indexes, RAG chunks)
│   │   │   ├── 39c4c834f1d6_create_file_metadata_table.py
│   │   │   ├── 703eb6e18ef3_add_rag_document_chunks.py
│   │   │   ├── 7a8f9c10e1d2_add_is_indexed_to_file_metadata.py
│   │   │   ├── 8f2a1b90c3d4_add_indexing_status_and_version.py
│   │   │   ├── 9b3c2a10d4e5_add_indexing_retry_and_error_fields.py
│   │   │   ├── ce06d2ee9755_phase6_jwt_and_mysql_upgrade.py
│   │   │   ├── ceaee9066079_add_next_retry_at_to_file_metadata.py
│   │   │   └── d9f1a2b3c4e5_add_file_metadata_indexes.py
│   │   ├── env.py                         # Migration runner environment
│   │   ├── script.py.mako                 # Revision template
│   │   └── README
│   ├── app/                               # Backend application source
│   │   ├── apis/routes/                   # HTTP Route controllers
│   │   │   ├── auth_routes.py             # /auth/register, /auth/login, /auth/verify-otp, /auth/resend-otp
│   │   │   ├── document_routes.py         # /files (presigned upload, metadata, delete, view)
│   │   │   ├── rag_document_routes.py     # /documents (RAG indexing trigger, status, chunk list)
│   │   │   ├── rag_routes.py              # /rag/query (SSE grounded answer streaming)
│   │   │   ├── search_routes.py           # /files/search (semantic search + SQL fallback)
│   │   │   └── system.py                  # /system/ping, /system/rag-metrics
│   │   ├── auth/                          # Authentication dependencies
│   │   │   └── auth_dependencies.py       # get_current_user, get_user_by_email
│   │   ├── core/                          # Settings & cryptography
│   │   │   ├── config.py                  # Pydantic Settings with env validation
│   │   │   └── security.py                # Argon2id password hashing & JWT encode/decode
│   │   ├── database/                      # SQLAlchemy database layer
│   │   │   ├── database.py                # Engine, connection pooling (size 20), get_db()
│   │   │   └── db_models.py               # User, FileMetadata, DocumentChunk, UserCorpusState
│   │   ├── schemas/                       # Pydantic validation schemas
│   │   │   ├── enums.py                   # FileStatus, IndexingStatus
│   │   │   ├── file.py                    # FileUploadRequest, PresignedUrlResponse, etc.
│   │   │   └── schemas.py                 # UserRegister, UserLogin, Token, UserOut, OTP schemas
│   │   ├── scripts/                       # Operational & evaluation runners
│   │   │   ├── rag_evaluate.py            # Offline RAG evaluation benchmark runner
│   │   │   ├── rag_phase2_smoke.py        # Pipeline Phase 2 smoke tests
│   │   │   ├── rag_phase5_smoke.py        # Pipeline Phase 5 smoke tests
│   │   │   └── set_cors_secure.py         # CORS policy helper
│   │   ├── services/                      # Core business logic services
│   │   │   ├── AI/                        # Vector services
│   │   │   │   ├── rag_vector_service.py  # Qdrant RAG chunk collection guard & schema check
│   │   │   │   └── vector_service.py      # Qdrant client & metadata vector search
│   │   │   ├── AWS/                       # Object storage services
│   │   │   │   └── s3_service.py          # Boto3 presigned PUT/GET, HEAD, delete
│   │   │   ├── cache/                     # Caching services
│   │   │   │   └── redis_cache.py         # Upstash Redis REST async client
│   │   │   ├── email/                     # Transactional email services
│   │   │   │   └── resend_service.py      # Resend REST client for OTP email delivery
│   │   │   └── rag/                       # Full RAG Pipeline modules
│   │   │       ├── answer_cache.py        # Fail-open Redis RAG answer cache + local fallback
│   │   │       ├── chunker.py             # 800-word / 100-word sliding window chunker
│   │   │       ├── config.py              # RAG pipeline tuning constants & settings
│   │   │       ├── document_processor.py  # Multi-format document parser coordinator
│   │   │       ├── embedding.py           # Gemini embedding caller (RETRIEVAL_DOCUMENT / RETRIEVAL_QUERY)
│   │   │       ├── extractors/            # Format-specific extractors
│   │   │       │   ├── docx_extractor.py  # DOCX/DOC paragraph extractor
│   │   │       │   ├── tabular_extractor.py # CSV/TSV/XLSX/XLS tabular extractor
│   │   │       │   └── text_extractor.py  # TXT/MD/LOG/RTF text extractor
│   │   │       ├── generation.py          # Gemini answer generation stream & citation validation
│   │   │       ├── ids.py                 # Deterministic UUIDv5 generator
│   │   │       ├── indexing_service.py    # Zero-downtime versioned index cutover
│   │   │       ├── inspection.py          # Vector & chunk inspection utilities
│   │   │       ├── metrics.py             # Retrieval evaluation metrics (Recall, MRR, NDCG)
│   │   │       ├── pdf_extractor.py       # PyMuPDF text & page extractor
│   │   │       ├── persistence.py         # Staging chunks to MySQL & index activation
│   │   │       ├── qdrant_service.py      # Chunk upsert with resume, vector cleanup
│   │   │       ├── query_service.py       # Qdrant chunk search & MySQL hydration
│   │   │       ├── query_utils.py         # Cache-key hashing, file validation, budget filters
│   │   │       ├── rag_orchestrator.py    # End-to-end RAG async generator (retrieve->hydrate->generate)
│   │   │       ├── retry.py               # Exponential backoff retry decorators
│   │   │       ├── runtime_metrics.py     # In-process RAG stage timing & counters
│   │   │       ├── schemas.py             # Internal RAG data models
│   │   │       └── text_cleaner.py        # Whitespace normalizer & repeated header stripper
│   │   ├── utils/                         # Utilities
│   │   │   └── rate_limiter.py            # Search rate limiter
│   │   └── workers/                       # Background task workers
│   │       ├── indexing_worker.py         # Metadata vector sync & crash recovery backfill
│   │       └── rag_worker.py              # Asynchronous RAG chunking & indexing worker
│   ├── data/                              # Evaluation and benchmark datasets
│   │   ├── eval_cases.jsonl               # Standardized RAG evaluation test cases
│   │   └── eval_report.json               # Benchmark performance output report
│   ├── streamlit_app/                     # Interactive admin & evaluation UI
│   │   └── app.py                         # Streamlit dashboard for chunk/index verification
│   ├── tests/                             # Automated test suite
│   │   ├── AI-test/                       # Vector & semantic search evaluation
│   │   │   ├── evaluate_search.py
│   │   │   ├── generate_dataset.py
│   │   │   └── golden_dataset.json
│   │   ├── unit/                          # Unit tests
│   │   │   ├── core/
│   │   │   │   └── test_config.py
│   │   │   └── services/
│   │   │       ├── rag/                   # 19 dedicated RAG module unit tests
│   │   │       │   ├── test_answer_cache.py
│   │   │       │   ├── test_chunker.py
│   │   │       │   ├── test_document_processor.py
│   │   │       │   ├── test_embedding.py
│   │   │       │   ├── test_generation.py
│   │   │       │   ├── test_ids.py
│   │   │       │   ├── test_indexing_service.py
│   │   │       │   ├── test_inspection.py
│   │   │       │   ├── test_metrics.py
│   │   │       │   ├── test_orchestrator.py
│   │   │       │   ├── test_orchestrator_metrics.py
│   │   │       │   ├── test_pdf_extractor.py
│   │   │       │   ├── test_persistence.py
│   │   │       │   ├── test_phase4_integration.py
│   │   │       │   ├── test_qdrant_service.py
│   │   │       │   ├── test_query_service.py
│   │   │       │   ├── test_query_utils.py
│   │   │       │   ├── test_retry.py
│   │   │       │   ├── test_runtime_metrics.py
│   │   │       │   ├── test_schemas.py
│   │   │       │   └── test_text_cleaner.py
│   │   │       ├── test_rag_vector_service.py
│   │   │       ├── test_redis_cache.py
│   │   │       ├── test_s3_service.py
│   │   │       └── test_vector_service.py
│   │   ├── integration/                   # Integration and scenario tests
│   │   │   ├── routes/
│   │   │   │   ├── conftest.py
│   │   │   │   ├── test_pagination.py
│   │   │   │   ├── test_rag_document_routes.py
│   │   │   │   ├── test_rag_metrics.py
│   │   │   │   ├── test_rag_routes.py
│   │   │   │   ├── test_semantic_search.py
│   │   │   │   ├── test_system.py
│   │   │   │   └── test_upload_file.py
│   │   │   ├── scenarios/
│   │   │   │   ├── test_outage_behavior.py
│   │   │   │   ├── test_partial_write_retry.py
│   │   │   │   ├── test_reindex_during_query.py
│   │   │   │   └── test_revocation.py
│   │   │   └── security/
│   │   │       ├── conftest.py
│   │   │       ├── test_auth_no_token.py
│   │   │       ├── test_cache_isolation.py
│   │   │       ├── test_guessed_ids.py
│   │   │       ├── test_payload_tampering.py
│   │   │       └── test_tenant_isolation.py
│   ├── alembic.ini                        # Alembic CLI configuration
│   ├── Dockerfile                         # Python 3.12-slim container specification
│   ├── main.py                            # FastAPI entry point, lifespan, CORS, router mounts
│   ├── pyproject.toml                     # Python package build specifications
│   ├── pytest.ini                         # Pytest configuration
│   ├── requirements.txt                   # Pinned backend dependencies
│   └── uv.lock                            # UV dependency lock file
├── frontend/                              # React 19 + Vite frontend
│   ├── public/                            # Static assets and icons
│   │   ├── favicon.svg                    # Brand favicon
│   │   └── icons.svg                      # SVG icon sprites
│   ├── src/                               # Application source code
│   │   ├── apis/                          # Network client layer (Axios only)
│   │   │   ├── __tests__/                 # Frontend API unit tests
│   │   │   │   └── ragApi.test.js
│   │   │   ├── authApi.js                 # loginUser(), registerUser(), verifyOtp(), resendOtp()
│   │   │   ├── axiosClient.js             # Base Axios instance, JWT interceptor, 401 reload
│   │   │   ├── documentApi.js             # getUploadUrl(), completeUpload(), fetchFiles(), deleteFile(), searchDocuments()
│   │   │   ├── ragApi.js                  # streamRagQuery(), parseRagSse(), parseSseEvents()
│   │   │   └── systemApi.js               # pingSystem()
│   │   ├── assets/                        # Bundled media & branding assets
│   │   │   ├── hero.png                   # Landing page hero graphics
│   │   │   ├── react.svg                  # React asset
│   │   │   ├── swan_drive_logo.svg        # Official Swan Drive SVG brand logo
│   │   │   └── vite.svg                   # Vite asset
│   │   ├── components/                    # Reusable MUI presentation components
│   │   │   ├── DeleteConfirmDialog.jsx    # File deletion modal
│   │   │   ├── EditMetadataDialog.jsx     # Title/tag/description editor modal
│   │   │   ├── FileList.jsx               # Vault file table container
│   │   │   ├── FileRow.jsx                # Single file row with status chip & actions
│   │   │   ├── Header.jsx                 # App header with backend status dot & theme toggle
│   │   │   ├── RagMessageBubble.jsx       # Chat message renderer with citation pills
│   │   │   ├── SearchHeader.jsx           # Search bar + upload dropzone trigger
│   │   │   ├── SelectionToolbar.jsx       # Batch file selection bar for Knowledge Base
│   │   │   ├── Sidebar.jsx                # Collapsible navigation drawer (Vault / Knowledge Base)
│   │   │   └── ToastNotification.jsx      # Global alert/toast wrapper
│   │   ├── context/                       # Shared React Context
│   │   │   └── KnowledgeBaseFilesContext.jsx # Document selection state shared between Vault & KB
│   │   ├── pages/                         # Top-level screen views
│   │   │   ├── AuthPage.jsx               # Login, Register & OTP verification forms
│   │   │   ├── KnowledgeBase.jsx          # Swan AI conversational RAG chat interface
│   │   │   ├── LandingPage.jsx            # Public landing marketing page
│   │   │   └── VaultPage.jsx              # Document vault management (upload, list, search)
│   │   ├── services/                      # Frontend pure business logic
│   │   │   ├── __tests__/                 # Frontend service unit tests
│   │   │   │   ├── ragHistoryService.test.js
│   │   │   │   ├── ragService.test.js
│   │   │   │   └── vaultSelectionService.test.js
│   │   │   ├── authService.js             # LocalStorage token persistence & JWT decoder
│   │   │   ├── ragHistoryService.js       # Chat session localStorage persistence
│   │   │   ├── ragService.js              # Selection rules (max 5 files, INDEXED filter)
│   │   │   └── vaultSelectionService.js   # Batch selection partitioning helpers
│   │   ├── theme.js                       # MUI theme definition (Swan Orange #FF6B00, Water Blue #0284C7)
│   │   ├── App.jsx                        # View router, dynamic titles & auth state root
│   │   └── main.jsx                       # Vite React 19 DOM mount point
│   ├── eslint.config.js                   # ESLint configuration
│   ├── index.html                         # SPA HTML entry point
│   ├── package.json                       # Frontend dependencies & scripts
│   ├── package-lock.json                  # NPM lock file
│   ├── README.md                          # Frontend documentation
│   ├── vercel.json                        # Edge API rewrite proxy to https://api.swandrive.me
│   └── vite.config.js                     # Vite build configuration
├── test-reports/                          # Automated testing reports & quality assurance documentation
│   ├── backend-test/                      # Backend Pytest & AI retrieval benchmark reports
│   │   ├── AI_search_evaluation.md        # AI search accuracy evaluation report (15 queries, 100 seeded docs)
│   │   └── full_backend_test_report.md    # Complete Pytest execution log (417/417 passed)
│   ├── frontend-test/                     # Frontend Vitest automated test logs
│   │   └── frontend_test_report_1.md      # Vitest execution log (39/39 passed)
│   └── test_summary.md                    # Consolidated QA testing summary for recruiters & contributors
├── others/                                # Configuration templates & environment files
│   └── .env.example                       # Documented environment variable template
├── Contributions/                         # Developer guidelines & contribution docs
├── documentation/                         # Project architecture, plans & tech specs
├── project-images/                        # System screenshots & UI mockups
├── docker-compose.yml                     # Production backend container config
├── GEMINI.md                              # Gemini & AI Assistant initialization directives
├── LICENSE                                # AGPL-3.0 License
├── README.md                              # Main public repository README
└── SECURITY.md                            # Security and vulnerability reporting policy
```

### Responsibility → Exact File Path Lookup Map

| System Responsibility | Primary Implementation File |
| :--- | :--- |
| **FastAPI Entrypoint, Middleware & Lifespan** | `backend/main.py` |
| **Application Settings & Env Schema** | `backend/app/core/config.py` |
| **Auth Hashing (Argon2id) & JWT Minting** | `backend/app/core/security.py` |
| **Current User Dependency & Token Guard** | `backend/app/auth/auth_dependencies.py` |
| **Auth Routes (`/auth/register`, `/auth/login`, `/auth/verify-otp`, `/auth/resend-otp`)** | `backend/app/apis/routes/auth_routes.py` |
| **Resend Email Service (Transactional OTP)** | `backend/app/services/email/resend_service.py` |
| **Document Vault Routes (`/files/*`)** | `backend/app/apis/routes/document_routes.py` |
| **Semantic & SQL Search Routes (`/files/search`)** | `backend/app/apis/routes/search_routes.py` |
| **RAG Streaming Query Route (`/rag/query`)**| `backend/app/apis/routes/rag_routes.py` |
| **RAG Document Routes (`/documents/*`)**| `backend/app/apis/routes/rag_document_routes.py` |
| **System Health & Metrics Routes (`/system/*`)** | `backend/app/apis/routes/system.py` |
| **SQLAlchemy Engine, Connection Pool & Session** | `backend/app/database/database.py` |
| **Relational ORM Models (`User`, `FileMetadata`, `DocumentChunk`, `UserCorpusState`)** | `backend/app/database/db_models.py` |
| **AWS S3 / B2 Presigned URLs & IO** | `backend/app/services/AWS/s3_service.py` |
| **Upstash Redis REST Client** | `backend/app/services/cache/redis_cache.py` |
| **File Metadata Qdrant Vector Search** | `backend/app/services/AI/vector_service.py` |
| **RAG Chunk Qdrant Collection Guard**| `backend/app/services/AI/rag_vector_service.py` |
| **RAG Query Orchestrator** | `backend/app/services/rag/rag_orchestrator.py` |
| **Document Extraction & Chunking Coordinator** | `backend/app/services/rag/document_processor.py` |
| **Format Extractors (DOCX, Tables, Text, PDF)** | `backend/app/services/rag/extractors/` & `backend/app/services/rag/pdf_extractor.py` |
| **RAG Zero-Downtime Indexing Cutover** | `backend/app/services/rag/indexing_service.py` |
| **Gemini Answer Generation & Citations**| `backend/app/services/rag/generation.py` |
| **Fail-Open Redis Answer Caching** | `backend/app/services/rag/answer_cache.py` |
| **RAG Background Indexing Worker** | `backend/app/workers/rag_worker.py` |
| **Metadata Vector Worker & Recovery** | `backend/app/workers/indexing_worker.py` |
| **Offline RAG Benchmark & Evaluation Runner** | `backend/app/scripts/rag_evaluate.py` |
| **Offline Evaluation Dataset & Cases** | `backend/data/eval_cases.jsonl` |
| **Streamlit Inspection & Evaluation UI** | `backend/streamlit_app/app.py` |
| **Backend Test Suite (Unit, Integration, AI-test)**| `backend/tests/` |
| **Frontend Axios Client & Interceptors**| `frontend/src/apis/axiosClient.js` |
| **Frontend Auth API (`loginUser`, `registerUser`, `verifyOtp`, `resendOtp`)** | `frontend/src/apis/authApi.js` |
| **Frontend Document API** | `frontend/src/apis/documentApi.js` |
| **Frontend RAG SSE Client** | `frontend/src/apis/ragApi.js` |
| **Frontend Auth State Service (Token Storage & Decode)** | `frontend/src/services/authService.js` |
| **Frontend RAG Selection Rules (Max 5, INDEXED)**| `frontend/src/services/ragService.js` |
| **Frontend Shared Selection Context** | `frontend/src/context/KnowledgeBaseFilesContext.jsx` |
| **Frontend Media & Brand Assets** | `frontend/src/assets/` |
| **Frontend Public Icons & Favicons** | `frontend/public/` |
| **Frontend MUI Theme Tokens** | `frontend/src/theme.js` |
| **Frontend Root, Routing & Dynamic Titles** | `frontend/src/App.jsx` |
| **Frontend Vault Page** | `frontend/src/pages/VaultPage.jsx` |
| **Frontend Swan AI Chat Page** | `frontend/src/pages/KnowledgeBase.jsx` |
| **Frontend Landing Marketing Page** | `frontend/src/pages/LandingPage.jsx` |
| **Frontend Auth Page (Login / Register / OTP)** | `frontend/src/pages/AuthPage.jsx` |
| **QA Testing Summary Overview** | `test-reports/test_summary.md` |
| **Backend Pytest Full Execution Report** | `test-reports/backend-test/full_backend_test_report.md` |
| **AI Search Accuracy Evaluation Report** | `test-reports/backend-test/AI_search_evaluation.md` |
| **Frontend Vitest Automated Test Report** | `test-reports/frontend-test/frontend_test_report_1.md` |

---

## 3. Overall System Architecture

```text
  [Browser: React 19 + MUI (Swan Drive SPA)]
          │
          │ HTTPS (JWT in Authorization Header)
          ▼
  [Vercel Edge Global CDN]
          │  Rewrite: /backend/:path* -> https://api.swandrive.me/:path*
          ▼
  [AWS EC2 (Ubuntu t3.micro)]
     └─► [Nginx Reverse Proxy (:80)]  (proxy_buffering off; client_max_body_size 50M)
              │  HTTP (127.0.0.1:8000)
              ▼
         [Docker Container: pkb-backend]
         (FastAPI + Gunicorn / 2 Workers)
              │
    ┌─────────┼──────────────────────────┬─────────────────────────┬─────────────────────────┐
    │         │                          │                         │                         │
    ▼         ▼                          ▼                         ▼                         ▼
[AWS RDS] [AWS S3 / B2]           [Qdrant Cloud]            [Upstash Redis]           [Resend API]
(MySQL 8) (Object Store)          (Vector Store)            (Serverless REST)         (Email Service)
• Users   • Presigned PUT/GET     • document_vault          • Fail-open answer cache  • Verification OTPs
• Files   • Raw Document Bytes    • document_chunks_v1      • Cache key with rev      • HTML templates
• Chunks                          • 768d Cosine             • TTL: 86400s (24h)
• Rev
    │
    ├─────────────────► [Google Gemini API]
    │                   • gemini-embedding-2 (768d, Cosine)
    │                   • Streaming Generation: gemini-3.1-flash-lite (multi-model fallback list)
    │
    └─────────────────► [Developer Admin Playground: Streamlit App]
                        • backend/streamlit_app/app.py (Thin HTTP client to backend)
```

---

## 4. Backend AI & RAG Architecture

### Ingestion & Indexing Pipeline (`rag_worker.py` + `indexing_service.py`)
1. **Trigger:** `POST /files/upload-complete` or `POST /documents` enqueues background worker task.
2. **Atomic Claim:** Worker claims file in MySQL (`indexing_status = EXTRACTING`, preventing concurrent duplicate runs).
3. **Download & Bounded Read:** Streams file bytes from S3/B2 up to 50 MB limit (`read_object_bytes_limited`).
4. **Multi-Format Extraction (`document_processor.py`):**
   - `.pdf`: PyMuPDF (`pdf_extractor.py`) extracts clean page text and page numbers.
   - `.docx`, `.doc`: `docx_extractor.py` extracts structured paragraphs and sections.
   - `.csv`, `.tsv`, `.xlsx`, `.xls`: `tabular_extractor.py` formats tabular rows with column headers.
   - `.md`, `.txt`, `.log`, `.rtf`: `text_extractor.py` extracts raw text preserving line positions.
5. **Cleaning & Sliding Window Chunking (`chunker.py` + `text_cleaner.py`):**
   - Strips repeated headers/footers, normalizes unicode and whitespace.
   - Builds continuous word stream preserving source page boundaries.
   - Chunks text at **800 words** with **100-word overlap** (`words-800-overlap-100-v1`).
6. **MySQL Staging (`persistence.py`):** Chunks written to `document_chunks` table with `index_version = active_index_version + 1`. Not active yet.
7. **Embedding Generation (`embedding.py`):**
   - Calls `gemini-embedding-2` with `task_type="RETRIEVAL_DOCUMENT"`, dimension **768**.
   - Batched in chunks of 64 with exponential backoff (`RAG_BACKOFF_BASE=1.0`, max 5 retries).
8. **Qdrant Upsert (`qdrant_service.py`):**
   - Deterministic UUIDv5 point IDs: `uuid5(NAMESPACE_URL, f"{user_id}:{file_id}:{index_version}:{chunk_index}")`.
   - Payload: `{user_id, file_id, index_version, chunk_id, page_start, page_end, text_checksum}`.
   - Upsert with resume support: skips already-upserted vectors on retry.
9. **Zero-Downtime Cutover:**
   - Verifies Qdrant point count matches staged chunk count.
   - In a single short MySQL transaction: updates `FileMetadata.active_index_version`, sets `indexing_status="completed"`, and increments `UserCorpusState.corpus_revision`.
   - Corpus revision increment immediately invalidates old cached RAG answers.
   - Asynchronously purges obsolete vectors from Qdrant for earlier index versions.

### Query & Generation Pipeline (`rag_orchestrator.py` + `generation.py`)
1. **Validation (`query_utils.py`):** Validates authenticated user owns all scoped `file_ids` and they are active/indexed.
2. **Cache Key & Lookup (`answer_cache.py`):**
   - Deterministic SHA-256 hash: `rag:{user_id}:{corpus_revision}:{query_hash}` over question, file IDs, top_k, threshold, and model versions.
   - Checks Upstash Redis REST. If HIT, streams cached answer instantly and yields final event.
3. **Query Embedding & Retrieval (`query_service.py`):**
   - Embeds question with `task_type="RETRIEVAL_QUERY"" using `gemini-embedding-2` (768d).
   - Searches Qdrant `document_chunks_v1` with filter `{user_id, file_id IN file_ids, index_version}`. Top K default: 6 (max 20), Cosine threshold: 0.35.
4. **Hydration & Post-Processing:**
   - Hydrates chunk `clean_text` from MySQL in a single batch query (preserves vector rank).
   - Deduplicates overlapping hits; caps max contribution to 3 chunks per file.
   - Fits into context token budget (3000 tokens ≈ 12,000 characters).
5. **Streaming Generation (`generation.py`):**
   - Wraps chunks into explicit `SOURCE id=<uuid> file=<name> pages=<p> ... END SOURCE` blocks.
   - System instruction enforces strictly answering from source blocks and citing valid chunk IDs as `id=<chunk_id>`. Sources are treated as untrusted data.
   - Calls Google GenAI SDK `client.aio.models.generate_content_stream()` with dynamic model fallback routing: `gemini-3.1-flash-lite` -> `gemini-3.5-flash-lite` -> `gemini-2.5-flash-lite` -> `gemini-3-flash` -> `gemma-4-26b-a4b-it`.
   - Yields SSE events: `data: {"type": "token", "text": "..."}`.
6. **Citation Validation & Cache Write:**
   - Validates all citations in generated answer against retrieved chunk IDs; strips hallucinated IDs.
   - Stores completed answer in Upstash Redis (TTL: 86400s) and local process cache.
   - Yields SSE final event: `data: {"type": "final", "sources": [...], "diagnostics": {...}}`.

---

## 5. Database Design (MySQL 8 via SQLAlchemy)

Engine: InnoDB, Charset: utf8mb4. Migrations managed by Alembic.

### Core Tables & Schemas

```text
users
  id (INT PK AUTO_INCREMENT)
  email (VARCHAR(255) UNIQUE INDEX NOT NULL)
  hashed_password (VARCHAR(255) NOT NULL)       -- Argon2id hash
  status (VARCHAR(20) DEFAULT 'active')
  created_at (DATETIME NOT NULL)

file_metadata
  fileid (INT PK AUTO_INCREMENT)
  userid (INT FK -> users.id ON DELETE CASCADE NOT NULL INDEX)
  s3_key (VARCHAR(255) UNIQUE INDEX NOT NULL)    -- uploads/{uuid}_{clean_name}
  filename (VARCHAR(255) NOT NULL)
  content_type (VARCHAR(100))
  size_bytes (BIGINT)
  status (VARCHAR(20) DEFAULT 'pending' INDEX)   -- pending | active | failed
  title (VARCHAR(100)), description (VARCHAR(255)), tags (VARCHAR(50))
  is_indexed (BOOLEAN DEFAULT FALSE INDEX)
  indexing_status (VARCHAR(20) DEFAULT 'pending' INDEX) -- pending|extracting|embedding|indexing|completed|failed
  index_version (INT DEFAULT 1 NOT NULL)
  active_index_version (INT DEFAULT 0 NOT NULL)
  corpus_revision (BIGINT DEFAULT 0 NOT NULL)
  retry_count (INT DEFAULT 0), next_retry_at (DATETIME), last_error (VARCHAR(500))
  page_count (INT), extracted_word_count (INT), chunk_count (INT), indexed_chunk_count (INT)
  indexing_started_at (DATETIME), indexing_completed_at (DATETIME)
  rag_error_code (VARCHAR(64)), rag_error_message (VARCHAR(500))
  extraction_version, cleaning_version, chunking_version, embedding_model, embedding_dimensions
  created_at (DATETIME), updated_at (DATETIME)
  -- Indexes: idx_user_status, idx_user_status_fileid, idx_indexing_recovery

document_chunks
  id (BIGINT/INT PK AUTO_INCREMENT)
  file_id (INT FK -> file_metadata.fileid ON DELETE CASCADE NOT NULL)
  user_id (INT FK -> users.id ON DELETE CASCADE NOT NULL)
  index_version (INT NOT NULL)
  chunk_index (INT NOT NULL)
  chunk_id (VARCHAR(36) UNIQUE NOT NULL)         -- UUIDv5
  page_start (INT), page_end (INT), word_start (INT), word_end (INT), word_count (INT)
  text_checksum (VARCHAR(64) NOT NULL)
  clean_text (MEDIUMTEXT NOT NULL)
  extraction_version, cleaning_version, chunking_version, embedding_model, embedding_dimensions
  source_key (VARCHAR(1024)), original_filename (VARCHAR(255))
  created_at (DATETIME), updated_at (DATETIME)
  -- Constraints: UNIQUE(file_id, index_version, chunk_index)
  -- Indexes: ix_document_chunks_file_version, ix_document_chunks_hydrate(user_id, chunk_id, index_version)

user_corpus_state
  user_id (INT PK FK -> users.id ON DELETE CASCADE)
  corpus_revision (BIGINT DEFAULT 0 NOT NULL)    -- Monotonic counter for cache invalidation
  updated_at (DATETIME)
```

---

## 6. Cloud & External Services Integration

### AWS Infrastructure
- **AWS EC2 (`t3.micro`, Ubuntu):** Hosts backend compute inside single Docker container (`pkb-backend`). Memory footprint ~150MB. Nginx reverse proxies port 80 to container port 8000.
- **AWS RDS (MySQL 8.0 Free Tier):** Dedicated DB instance, connection pool size: 20, max overflow: 40, pool recycle: 1800s, pre-ping enabled.
- **AWS S3 / Backblaze B2:** Object store for uploaded documents.
  - Development/Testing uses Backblaze B2 (`AWS_ENDPOINT_URL` set).
  - Production uses AWS S3 (Standard, region `eu-north-1`).
  - Storage pattern: `uploads/{uuid4}_{sanitized_filename}`.
  - Direct presigned PUT upload; backend never buffers heavy binaries in memory.

### Qdrant Cloud (Vector DB)
- **Endpoint:** Managed cluster over HTTPS (`QDRANT_HOST` + `QDRANT_API_KEY`).
- **Collections:**
  - `document_vault`: File-level metadata vector search (768d, Cosine). Payload index: `user_id`, `tags`.
  - `document_chunks_v1`: RAG chunk vectors (768d, Cosine). Payload indexes: `user_id` (INT), `file_id` (INT), `index_version` (INT).
- **Tenant Isolation:** Every vector query and point deletion strictly filters by authenticated `user_id`.

### Upstash Redis (Serverless Cache)
- **Protocol:** HTTP REST endpoint via `httpx.AsyncClient` (no open persistent TCP sockets).
- **Keys:** `rag:{user_id}:{corpus_revision}:{query_sha256}`.
- **TTL:** 86,400 seconds (24 hours).
- **Fail-Open Policy:** If Redis times out or is unreachable, query generation proceeds normally without throwing HTTP 500.

### Resend API (Transactional Email)
- **Protocol:** HTTP REST endpoint via `httpx.AsyncClient`.
- **Use Case:** Delivers 6-digit verification OTP codes during registration and resend requests.

---

## 7. Frontend Architecture (React 19 + MUI)

### Component Tree & State Flow
- **Root (`App.jsx`):**
  - Manages session token (`localStorage.getItem('pkb_access_token')`) and decoded user info.
  - Manages theme mode (`light` / `dark`) and creates MUI theme via `createAppTheme(mode)`.
  - Unauthenticated view: `LandingPage` or `AuthPage` (sign in, sign up, OTP modal).
  - Authenticated view: Wraps app in `KnowledgeBaseFilesProvider`, renders `Header`, `Sidebar`, and toggles between `VaultPage` and `KnowledgeBase` via local view state.
  - Controls dynamic SEO page titles (`document.title`) and `meta[name="robots"]noindex, nofollow` protection for private views.
- **Shared Selection State (`KnowledgeBaseFilesContext.jsx`):**
  - Provides `selected` documents array, `addDocs`, `removeDoc`, and `clear`.
  - Backed by pure business logic in `ragService.js` (enforces max 5 documents, only `INDEXED` status).
- **Vault Page (`VaultPage.jsx`):**
  - Owns file list, pagination (limit 15), search bar (`SearchHeader`), metadata editing (`EditMetadataDialog`), and deletion dialog (`DeleteConfirmDialog`).
  - Batch selection toolbar (`SelectionToolbar`) enables multi-file selection and "Add to Swan AI" transfer.
- **Swan AI Knowledge Base (`KnowledgeBase.jsx`):**
  - Conversational chat UI grounded in selected documents.
  - Drives SSE streaming via `streamRagQuery()` in `ragApi.js`.
  - Displays streaming tokens, sources accordion, and verified citations in `RagMessageBubble` using `react-markdown` and `remark-gfm`.
  - Chat history persisted per-user in localStorage via `ragHistoryService.js`.

### Strict Styling & Separation of Concerns
- **React MUI Only:** All layout and styles use `@mui/material` components, theme design tokens, and the `sx` prop. Zero custom `.css`/`.scss` files.
- **Decoupled API Layer:** React components never make direct Axios calls or format query payloads. All networking is isolated in `src/apis/` and pure domain rules in `src/services/`.

---

## 8. Deployment & Runtime Configuration

### Production Architecture Topology

```text
[Vercel Edge CDN: React SPA]
       │
       │ HTTPS rewrite: /backend/:path* -> https://api.swandrive.me/:path*
       ▼
[AWS EC2 Host: Port 80]
  └─► [Nginx Host Proxy]
        ├── proxy_pass http://127.0.0.1:8000;
        ├── proxy_buffering off;          # Critical for SSE streaming
        └── client_max_body_size 50M;
              │
              ▼
  [Docker Container: pkb-backend]
  (gunicorn -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000 main:app)
              │
              ├── AWS RDS MySQL (Port 3306)
              ├── Qdrant Cloud (HTTPS: 443 / 6333)
              ├── Upstash Redis (HTTPS: 443)
              ├── AWS S3 (HTTPS: 443)
              ├── Resend API (HTTPS: 443)
              └── Google Gemini API (HTTPS: 443)
```

### Build & Startup Commands
- **Local / Container Backend Startup:**
  ```bash
  cd backend && uvicorn main:app --host 127.0.0.1 --port 8000 --reload
  # Production container command:
  gunicorn -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000 main:app
  ```
- **Database Migrations:**
  ```bash
  cd backend && alembic upgrade head
  ```
- **Frontend Development & Build:**
  ```bash
  cd frontend && npm run dev       # Starts Vite dev server (:5173)
  cd frontend && npm run build     # Outputs production assets to frontend/dist/
  ```
- **Docker Compose Deploy:**
  ```bash
  docker compose --env-file ./others/.env up -d --build
  ```
- **Configuration & Environment:**
  - Application settings live exclusively in `others/.env` (never committed to git).
  - Production environment variable names are documented in `others/.env.example`.
