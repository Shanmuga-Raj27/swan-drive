# Swan Drive — Project Overview & Architecture

Welcome to **Swan Drive**! This document provides an overview of Swan Drive's goals, architecture, technology stack, and community mission.

---

## 🌟 About Swan Drive

Swan Drive is an open-source personal knowledge management and cloud document workspace. Built as an end-to-end learning project, it demonstrates how modern cloud-native architectures, vector search, and Retrieval-Augmented Generation (RAG) combine into a responsive, real-world application.

### Core Capabilities

- **Secure Document Vault:** Upload and manage multi-format files (PDF, DOCX, XLSX, CSV, Markdown, TXT) with direct-to-cloud presigned transfers, preventing backend bottlenecks.
- **Semantic & Vector Search:** Natural language search powered by high-dimensional vector embeddings, allowing users to find documents based on meaning rather than just keywords.
- **Conversational RAG (Swan AI):** Select documents and ask questions. Swan AI generates answers grounded strictly in your documents with real-time Server-Sent Events (SSE) token streaming and verifiable citation references.
- **Multi-Tenant Isolation:** Rigorous tenant isolation ensures that every file, metadata record, chunk, and vector belongs exclusively to its authenticated owner.
- **Performance & Resilient Caching:** Fast responses via fail-open caching with Redis, automatically invalidating stale answers when documents change.

---

## 🎓 Learning and Collaboration

> "Swan Drive is a learning-oriented open-source project. Developers are welcome to explore its documentation and source code for learning, experimentation, and understanding real-world application development."

Swan Drive was designed to bridge the gap between simple tutorial apps and production-ready systems. We encourage contributors and curious developers to use this codebase as an opportunity to learn:

- **Frontend Engineering:** Modern component design with React 19, Material UI (MUI 9), accessibility, and responsive layouts without custom CSS overhead.
- **Backend Architecture:** Asynchronous API development with FastAPI, connection pooling, Alembic database migrations, and background workers.
- **Cloud Object Storage:** Presigned URL workflows (AWS S3 / Backblaze B2) for scalable file uploads.
- **Vector Search & AI:** Document parsing, text chunking, embedding generation (`gemini-embedding-2`), vector storage (Qdrant), and streaming LLM responses (`gemini-3.1-flash-lite`).
- **Application Security:** Argon2id password hashing, JWT authentication, tenant isolation, and safe handling of untrusted inputs.

You do **not** need years of professional experience to participate. Whether you are fixing a typo, improving a UI interaction, or proposing an architecture optimization, your curiosity and contributions are welcome.

### Accessible Local Setup & Free Tiers
Contributors can set up the entire stack locally using free developer tiers and local tools:
- Free S3-compatible cloud storage via **Backblaze B2**.
- Free serverless caching via **Upstash Redis**.
- Free vector indexing within **Qdrant Cloud** limits.
- Free AI embeddings and generation via **Google Gemini API keys**.
- Free transactional OTP email testing via **Resend** (using `onboarding@resend.dev` without a custom domain).
- Local **MySQL** database for structured data.
- Streamlined setup with **`uv sync`** for the backend and **`npm ci`** for the frontend (see `others/.env.example` and `project-rules.md` for configuration details).

Developers are also welcome to leverage any AI tools, coding assistants, and the Playwright MCP server to accelerate testing and development, provided they do so with caution and verify all changes against project standards.

---

## 🏗️ High-Level Technology Stack

Swan Drive uses a focused, modern technology stack:

| Domain | Technologies | Purpose |
| :--- | :--- | :--- |
| **Frontend UI** | React 19, Vite, Material UI (MUI 9) | Responsive Single-Page Application (SPA) with custom design tokens and dynamic views. |
| **Networking & State** | Axios, React Context | Centralized API communication, JWT handling, and shared cross-component selection state. |
| **Backend Framework** | Python 3.12, FastAPI, Pydantic | Asynchronous REST endpoints, schema validation, and SSE streaming. |
| **Relational Database** | MySQL 8.0, SQLAlchemy 2, Alembic | Structured data for users, document metadata, and staged document chunks. |
| **Vector Database** | Qdrant Cloud | High-performance vector indexing and semantic retrieval (768-dimensional Cosine similarity). |
| **AI & Embeddings** | Google Gemini API (Google GenAI SDK) | Document embeddings (`gemini-embedding-2`) and conversational answer generation (`gemini-3.1-flash-lite`). |
| **Cache & Ephemeral Data** | Upstash Redis (REST) | Fast RAG answer caching, OTP staging, and rate limiting. |
| **Object Storage** | AWS S3 / Backblaze B2 | Secure document binary storage via direct presigned upload/download URLs. |
| **Document Processing** | PyMuPDF & Custom Extractors | Multi-format text and metadata extraction preserving source page coordinates. |

---

## 🔄 How Swan Drive Works

1. **Direct Upload:** The client requests a presigned URL from FastAPI and uploads file binaries directly to object storage. File metadata is saved in MySQL.
2. **Background Ingestion:** Background workers extract text from uploaded files, clean formatting, partition text into overlapping chunks, generate vector embeddings, and upsert them to Qdrant.
3. **Conversational Query:** When querying Swan AI, the backend retrieves relevant text chunks from Qdrant, hydrates verified text from MySQL, and streams citation-backed answers from Google Gemini directly to the client via Server-Sent Events.

---

## 🚀 Actively Evolving

Swan Drive is actively developed and continuously improving. Features, documentation, and architecture patterns will evolve over time. We invite you to explore the other guides in this directory to get started:

- [`project-rules.md`](./project-rules.md) — Contributor expectations, coding standards, and pull request guidelines.
- [`frontend-guide.md`](../Frontend%20guide/frontend-guide.md) — Frontend architecture, Material UI design standards, and responsive UI conventions.
- [`security-guide.md`](../Security%20guide/security-guide.md) — Responsible security reviews, reporting procedures, and testing guidelines.
