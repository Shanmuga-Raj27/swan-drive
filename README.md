<div align="center">

<img src="project-images/swan_logo_final_clean.svg" alt="Swan Drive Logo" width="130" />

# Swan Drive

> **Swan Drive is an intelligent document workspace for everyone.**  
> **Cloud Document Storage with AI-powered document search.**  
> **Ask questions and understand your documents with Swan AI.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-swandrive.me-FF6B00?style=for-the-badge&logo=vercel&logoColor=white)](https://swandrive.me)
[![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![React 19](https://img.shields.io/badge/React%2019-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://react.dev)
[![Material UI](https://img.shields.io/badge/MUI%209-007FFF?style=for-the-badge&logo=mui&logoColor=white)](https://mui.com)
[![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20Search-DC2626?style=for-the-badge)](https://qdrant.tech)
[![License: AGPL-3.0](https://img.shields.io/badge/License-AGPL%20v3-blue.svg?style=for-the-badge)](LICENSE)

---

**🌐 Live Application:** [https://swandrive.me](https://swandrive.me)  
**📬 Developer Contact:** [Email](mailto:rshanmugaraj11@gmail.com) · [LinkedIn](https://www.linkedin.com/in/shanmugaraj27)

</div>

> `NOTE:`
> **Live Demo Backend Schedule:**  
> The frontend client is hosted 24/7 on **Vercel Edge CDN**. Due to AWS free-tier credit limits, the backend compute instance on **AWS EC2** is active daily from **9:30 AM to 4:00 PM IST (`Asia/Kolkata`)**.  
> If you visit outside these operational hours and find the backend paused, feel free to reach out directly via [Email](mailto:rshanmugaraj11@gmail.com) or [LinkedIn](https://www.linkedin.com/in/shanmugaraj27) to spin it up or schedule a live walkthrough!

---

## ✨ Key Features

- 📁 **Cloud Document Storage** — Upload and manage multi-format documents (PDF, DOCX, XLSX, CSV, Markdown, TXT) up to 50MB. File binaries stream straight to **AWS S3** via presigned URLs without bottlenecking the backend server.
- 🔍 **Semantic & Vector Document Search** — Beyond exact keywords: documents are indexed as high-dimensional vector embeddings, enabling natural language search across titles, tags, and document contents.
- 🧠 **Conversational Q&A with Swan AI (RAG)** — Select up to 5 documents and chat with **Swan AI**. Powered by Retrieval-Augmented Generation (RAG) and Google Gemini, delivering token-by-token streaming responses (SSE) grounded in your documents with verifiable citation pills.
- ⚡ **Fail-Open Answer Caching with Redis** — Repeated queries resolve instantly through an Upstash Redis REST cache. Cache keys dynamically bind to corpus revisions, guaranteeing instant invalidation whenever documents are updated.
- 🎨 **Modern, Responsive MUI Interface** — Clean single-page application crafted in React 19 and Material UI (MUI 9), featuring light/dark mode, real-time backend health indicators, and zero external CSS overhead.
- 🛡️ **Verified Authentication & Multi-Tenant Security** — Cryptographically secured with Argon2id password hashing, short-lived JWT bearer tokens, and row-level tenant isolation. Registration enforces two-step email verification via 6-digit OTPs delivered via **Resend**, backed by ephemeral Redis staging (10-minute TTL) and atomic Lua-scripted brute-force protection.

---

## 🛠️ Tech Stack

| Layer | Technologies & Libraries | Key Responsibilities |
| :--- | :--- | :--- |
| **Frontend** | **React 19**, **Vite**, **Material UI (MUI 9)**, **Axios** | Single-page app (SPA), custom MUI design tokens, JWT interceptors, and Server-Sent Events (SSE) streaming consumer. |
| **Backend** | **Python 3.12**, **FastAPI**, **Uvicorn / Gunicorn**, **Pydantic** | High-performance asynchronous REST API, request schema validation, streaming endpoints, and background task workers. |
| **Auth & Email** | **Resend API**, **Argon2id (`pwdlib`)**, **JWT (`python-jose`)** | Transactional OTP email delivery via Resend REST API; Argon2id password hashing and JWT bearer token issuance. |
| **AI / RAG & Vectors** | **Google GenAI SDK**, **Gemini API**, **Qdrant Cloud**, **PyMuPDF** | Vector embeddings via `gemini-embedding-2` (768d Cosine), streaming generation via Gemini multi-model fallback (`gemini-3.1-flash-lite`), and PDF parsing. |
| **Database & Cache** | **AWS RDS MySQL 8.0**, **SQLAlchemy 2**, **Alembic**, **Upstash Redis** | Relational user/file metadata and chunk staging with connection pooling; serverless REST-based fail-open query caching (24h TTL), ephemeral OTP staging (10m TTL), and login rate limiting. |
| **Cloud & DevOps** | **AWS EC2 (`t3.micro`)**, **AWS S3**, **Vercel Edge**, **Docker & Compose**, **Nginx** | Containerized backend deployment behind Nginx reverse proxy, client-direct S3 presigned transfers, and Vercel edge reverse-proxy rewrites. |

---

## 🏛️ High-Level Architecture

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   React 19 + MUI Client (SPA)                          │
│                     Hosted on Vercel Edge CDN                          │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ HTTPS (Bearer JWT / SSE Stream)
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                      AWS EC2 Host (Ubuntu Linux)                       │
│  ┌──────────────────────────────────────────────────────────────────┐  │
│  │                    Nginx Reverse Proxy (:80)                     │  │
│  │        (proxy_buffering off; client_max_body_size 50M)           │  │
│  └────────────────────────────────┬─────────────────────────────────┘  │
│                                   │ HTTP (127.0.0.1:8000)              │
│  ┌────────────────────────────────▼─────────────────────────────────┐  │
│  │               FastAPI Backend Container (Docker)                 │  │
│  │   • Auth & Security (Argon2id + JWT)                             │  │
│  │   • Transactional Email OTP (Resend API)                         │  │
│  │   • S3 Presigned URL Provisioning                                │  │
│  │   • Document Ingestion, PyMuPDF Parsing & Chunking               │  │
│  │   • RAG Orchestrator & SSE Token Streaming                       │  │
│  └────────────────────────────────┬─────────────────────────────────┘  │
└───────────────────────────────────┼────────────────────────────────────┘
                                    │
        ┌───────────────────────────┼───────────────────────────┐
        ▼                           ▼                           ▼
 ┌──────────────┐            ┌──────────────┐            ┌──────────────┐
 │   AWS RDS    │            │ Qdrant Cloud │            │ Redis Cloud  │
 │ (MySQL 8.0)  │            │(Vector Store)│            │(Upstash REST)│
 │ • Users/Auth │            │• Doc Vectors │            │• OTP Staging │
 │ • File Meta  │            │• Chunks (768d│            │• Rate Limits │
 │ • Chunks/Rev │            │  Cosine)     │            │• Answer Cache│
 └──────────────┘            └──────────────┘            └──────────────┘
        │                           │
        ▼                           ▼
 ┌──────────────┐            ┌──────────────────────────────────────────┐
 │    AWS S3    │            │            Google Gemini API             │
 │(Object Store)│            │ • Embeddings: gemini-embedding-2 (768d)  │
 │ • Raw Files  │            │ • Generation: gemini-3.1-flash-lite      │
 │ • Presigned  │            │   (Dynamic multi-model fallback chain)   │
 │   PUT / GET  │            └──────────────────────────────────────────┘
 └──────────────┘
```

---

## 🔄 How the Application Works

```text
 [User / Browser]
        │
        ├─► 1. Upload & Store ────────► [AWS S3] (Presigned PUT) + [MySQL] (Metadata)
        │
        ├─► 2. Process & Index ───────► [FastAPI Worker] ──► [PyMuPDF Chunker] ──► [Qdrant Cloud] (768d)
        │
        └─► 3. Ask Swan AI (Query) ───► [Redis Cache] ──(Hit)──► Instant Stream
                                             │ (Miss)
                                             ├──► [Qdrant Cloud] (Retrieve Top-K Chunks)
                                             ├──► [MySQL] (Hydrate Verified Text)
                                             └──► [Gemini LLM] (SSE Grounded Stream + Citations)
```

1. **Upload & Store:** When a user uploads a document, the frontend requests a secure presigned upload URL from FastAPI and streams the binary directly to **AWS S3**. The backend never buffers heavy files, maintaining high throughput. Document metadata is registered in **AWS RDS MySQL**.

2. **Index & Embed:** An asynchronous background worker extracts the document text (preserving page coordinates), normalizes formatting, and splits text into 800-word sliding windows (100-word overlap). Vectors are generated via `gemini-embedding-2` and upserted into **Qdrant Cloud** with a zero-downtime versioned cutover.

3. **Search & Ask Swan AI:** Users can search files semantically or pick up to 5 documents to query with **Swan AI**. The orchestrator checks **Upstash Redis** for a cached response; on a cache miss, it retrieves the most relevant chunks from Qdrant, hydrates clean text from MySQL, and prompts **Google Gemini** to stream back citation-backed answers via Server-Sent Events (SSE).

---

## 📁 Project Folder Structure

```text
swan-drive/
├── backend/                              # FastAPI backend application
│   ├── alembic/                          # Database migration scripts & configurations
│   │   ├── versions/                     # Versioned schema migration files
│   │   └── env.py                        # Alembic migration environment runner
│   ├── app/                              # Main application package
│   │   ├── apis/routes/                  # API endpoints (auth, document, search, rag, system)
│   │   ├── auth/                         # Authentication dependencies & user lookup
│   │   ├── core/                         # Configuration settings & cryptographic security
│   │   ├── database/                     # SQLAlchemy engine, session maker & ORM models
│   │   ├── schemas/                      # Pydantic validation schemas & domain enums
│   │   ├── scripts/                      # Evaluation and operational smoke test scripts
│   │   ├── services/                     # Business logic modules
│   │   │   ├── AI/                       # Qdrant client & vector operations
│   │   │   ├── AWS/                      # S3 presigned URL generation & storage ops
│   │   │   ├── cache/                    # Upstash Redis REST cache client
│   │   │   ├── email/                    # Resend transactional email OTP service
│   │   │   └── rag/                      # RAG ingestion, chunking, embeddings & query orchestrator
│   │   │       └── extractors/           # Multi-format document parsers (PDF, DOCX, tables, text)
│   │   ├── utils/                        # Rate limiting and helper utilities
│   │   └── workers/                      # Background indexing & recovery workers
│   ├── data/                             # Standardized evaluation test cases & reports
│   ├── streamlit_app/                    # Streamlit inspection & evaluation dashboard
│   ├── tests/                            # Automated test suite (unit, integration & AI-test)
│   ├── alembic.ini                       # Alembic CLI configuration
│   ├── Dockerfile                        # Backend production container specification
│   ├── main.py                           # FastAPI entrypoint, middleware & lifespan
│   ├── pyproject.toml                    # Python project packaging specifications
│   └── requirements.txt                  # Pinned Python package dependencies
├── frontend/                             # React 19 + Vite frontend application
│   ├── public/                           # Static assets, icons & favicons
│   ├── src/                              # Frontend source code
│   │   ├── apis/                         # Axios client, auth, files & RAG SSE APIs
│   │   ├── assets/                       # Brand logo, hero graphics & SVG media
│   │   ├── components/                   # Reusable MUI presentation components
│   │   ├── context/                      # React context (document selection state)
│   │   ├── pages/                        # Swan Drive (VaultPage), KnowledgeBase (Swan AI), Auth & Landing
│   │   ├── services/                     # Pure domain logic, token persistence & validators
│   │   ├── App.jsx                       # Root routing & authentication state manager
│   │   ├── main.jsx                      # Vite React 19 DOM mount point
│   │   └── theme.js                      # Custom Material UI theme & design tokens
│   ├── index.html                        # Single-page application HTML entrypoint
│   ├── package.json                      # Frontend scripts and npm dependencies
│   ├── vercel.json                       # Edge rewrite proxy configuration
│   └── vite.config.js                    # Vite build configuration
├── others/                               # Configuration templates & environments
│   └── .env.example                      # Example environment variables template
└── docker-compose.yml                    # Multi-container local & production orchestration
```

---

## 🤝 Open Source & Contributing

Contributions, feedback, and suggestions are warmly welcomed! Whether you're fixing a bug, proposing a new feature, improving documentation, or optimizing RAG retrieval:

1. **Fork** the repository
2. **Create** your feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add some amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

Feel free to open an Issue to report bugs or discuss roadmap ideas.

---

## 📄 License

This project is open-source software licensed under the [GNU Affero General Public License v3.0 (AGPL-3.0)](LICENSE).
