# Swan Drive — Local Setup & Contributor Guide

Welcome to the **Swan Drive Local Setup Guide**! This document provides a step-by-step walkthrough for setting up, configuring, and running the entire Swan Drive stack on your local machine for development and testing.

---

## 💡 Free-Tier Guarantee: No Credit Card Required

All third-party cloud services required to run Swan Drive locally offer **100% free tiers with no credit card required**:

| Service | Free Tier Allowance | Purpose in Swan Drive |
| :--- | :--- | :--- |
| **Backblaze B2** | 10 GB free cloud storage | S3-compatible document uploads and presigned URLs |
| **Upstash Redis** | 10,000 commands/day free | Serverless REST cache for RAG answers & rate limiting |
| **Qdrant Cloud** | 1 GB free vector cluster | High-dimensional vector storage & semantic search |
| **Google Gemini API** | Free tier rate limits | Document embeddings (`gemini-embedding-2`) and RAG generation (`gemini-3.5-flash-lite`) |
| **Resend** | 3,000 emails/month free | Transactional 6-digit OTP registration emails |
| **Local MySQL** | Free, runs on your machine | Relational database for users, files, and chunks |

---

## 📋 Prerequisites

Before getting started, make sure you have the following installed on your machine:

1. **Git:** For version control and branch management.
2. **Python 3.12+:** The backend runtime.
3. **`uv`:** Ultra-fast Python package and environment manager.
4. **Node.js (v18+ or v20+) & npm:** The frontend JavaScript runtime.
5. **MySQL 8.0:** Running locally on port `3306` (native service or Docker container).

---

## 🌿 Git Fork, Clone & Branching Workflow

Contributors must always work on a dedicated **feature branch** and never commit directly to `main`.

### 1. Fork the Repository

Click the **Fork** button on the official GitHub repository page to create a personal copy under your GitHub account.

### 2. Clone Your Fork Locally

Clone your fork to your computer:

```bash
git clone https://github.com/CONTRIBUTOR_USERNAME/swan-drive.git
cd swan-drive
```

### 3. Configure Upstream Remote

Connect your local repository to the official Swan Drive repository so you can receive future updates.

```bash
git remote add upstream https://github.com/Shanmuga-Raj27/swan-drive.git
git remote -v
```

You should see two remotes:

- `origin` → Your personal GitHub fork.
- `upstream` → Official Swan Drive repository.

### 4. Sync Main and Create a Feature Branch

Before starting work, make sure your local `main` branch is updated with the official repository.

```bash
git switch main
git fetch upstream
git merge --ff-only upstream/main
git switch -c feature/your-feature-name
```

**Branch naming conventions:**

- `feature/` – New features or improvements.
- `fix/` – Bug fixes.
- `docs/` – Documentation improvements.
- `refactor/` – Code restructuring without changing functionality.

### 5. Commit and Push Changes

After completing your work, review your changes and commit them.

```bash
git status
git add <modified-files>
git commit -m "feat(scope): concise description of changes"
git push -u origin feature/your-feature-name
```

### 6. Open a Pull Request

1. Visit your fork on GitHub.
2. Open a Pull Request.
3. Set the **base repository** to the official Swan Drive repository.
4. Set the **base branch** to `main`.
5. Set the **head repository** to your fork.
6. Set the **compare branch** to your feature branch.
7. Describe your changes and submit the Pull Request.

Wait for project maintainer review before merging.

---

## ⚙️ Environment Configuration (`others/.env`)

The backend loads configuration from `others/.env`. A complete template is provided in `others/.env.example`.

### Step 1: Create Your `.env` File
From the repository root:
```bash
cp others/.env.example others/.env
```

### Step 2: Configure Free-Tier Secrets in `others/.env`

Open `others/.env` in your text editor and fill in your actual values:

#### 1. Core Security & API URL
```env
SECRET_KEY=your_random_secret_key_here
VITE_API_URL=http://localhost:8000
```
*(You can generate a random secret key with `openssl rand -hex 32`).*

#### 2. Local MySQL Database
Ensure a database named `personal_knowledge_base` exists in your local MySQL instance:

Configure your connection string:
```env
DATABASE_URL=mysql+pymysql://your_username:your_mysql_password@localhost:3306/personal_knowledge_base
```

#### 3. Backblaze B2 Storage (Using Exact AWS Variable Names)
Backblaze B2 provides S3-compatible APIs. Use the exact `AWS_*` variables in `others/.env`:
- Create a free account at [backblaze.com/b2](https://www.backblaze.com/b2).
- Create a Bucket (e.g., `my-swandrive-dev-bucket`).
- Create an **Application Key** with read/write access to that bucket.
- Fill in:
```env
AWS_ACCESS_KEY_ID=your_b2_key_id
AWS_SECRET_ACCESS_KEY=your_b2_application_key
AWS_REGION=your_bucket_region_here
AWS_ENDPOINT_URL=https://s3.your-region.backblazeb2.com
S3_BUCKET_NAME=your_bucket_name
S3_PRESIGNED_URL_EXPIRY=3600
```

#### 4. Google Gemini API (AI Embeddings & Generation)
- Get a free API key at [aistudio.google.com](https://aistudio.google.com).
- Fill in:
```env
GEMINI_API_KEY=your_gemini_api_key_here
GEMINI_EMBEDDING_MODEL=gemini-embedding-2
GEMINI_GENERATION_MODEL=gemini-3.1-flash-lite,gemini-3.5-flash-lite,gemini-2.5-flash-lite,gemini-3-flash,gemma-4-26b-a4b-it
EMBEDDING_DIMENSIONS=768
```

#### 5. Qdrant Cloud (Vector Database)
- Sign up for a free 1GB cluster at [cloud.qdrant.io](https://cloud.qdrant.io).
- Grab your cluster URL endpoint and API key from the cluster dashboard:
```env
QDRANT_HOST=https://your-cluster-id.eu-central.aws.cloud.qdrant.io:6333
QDRANT_API_KEY=your_qdrant_api_key_here
QDRANT_COLLECTION_NAME=document_vault
QDRANT_RAG_COLLECTION_NAME=document_chunks_v1
QDRANT_DISTANCE=COSINE
```

#### 6. Upstash Redis (Caching & Rate Limiting)
- Create a free serverless Redis database at [console.upstash.com](https://console.upstash.com).
- Copy the REST URL and REST token:
```env
RAG_CACHE_ENABLED=true
UPSTASH_REDIS_REST_URL=https://your-database.upstash.io
UPSTASH_REDIS_REST_TOKEN=your_upstash_rest_token_here
RAG_CACHE_TTL_SECONDS=3600
```

#### 7. Resend (Email OTP Verification)
- Sign up for a free account at [resend.com](https://resend.com) with your personal email.
- Generate an API key in the Resend dashboard.
- Use the built-in testing sender `onboarding@resend.dev` (no custom domain required):
```env
RESEND_API_KEY=re_your_resend_api_key_here
RESEND_FROM_EMAIL=Swan Drive <onboarding@resend.dev>
```
> **Important:** In Resend's free testing mode, emails can only be delivered to the **same email address you used to register on Resend**. When testing registration on `localhost`, sign up using that exact email address.

---

## 🐍 Backend Setup & Execution

### 1. Synchronize Python Environment
Navigate to the `backend/` directory and run `uv sync`:
```bash
cd backend
uv sync
```
This automatically sets up a virtual environment and installs all pinned dependencies.

### 2. Run Database Migrations
Run Alembic migrations to create all database tables in your local MySQL instance:
```bash
uv run alembic upgrade head
```

### 3. Start the FastAPI Development Server
Start the backend server with auto-reload:
```bash
uv run uvicorn main:app --host 127.0.0.1 --port 8000 --reload
```
- The backend is now running at: `http://localhost:8000`
- API documentation (Swagger UI) is accessible at: `http://localhost:8000/docs`
- Health check endpoint: `http://localhost:8000/system/ping`

---

## ⚛️ Frontend Setup & Execution

In a **separate terminal window**:

### 1. Install Node Dependencies
Navigate to the `frontend/` directory and install exact pinned dependencies cleanly:
```bash
cd frontend
npm ci
```

### 2. Start the Vite Development Server
Start the frontend development server:
```bash
npm run dev
```
- The application will be live at: `http://localhost:5173`

---

## 🧪 Testing the Complete Local Flow

1. Open `http://localhost:5173` in your web browser.
2. Click **Get Started** or **Register**.
3. Register using the **same email address registered on your Resend account**.
4. Check your inbox for the 6-digit OTP code and enter it on the verification screen.
5. Log in to your new account.
6. In **Swan Drive**, click **Upload file** and upload a small PDF, Markdown, or TXT file to verify that Backblaze B2 storage upload, PyMuPDF extraction, and Qdrant vector indexing work.
7. Select the uploaded file and switch to **Swan AI**. Ask a question to verify that Qdrant retrieval, Google Gemini answer generation, and Upstash Redis caching succeed.

---

## 🛠️ Common Troubleshooting Tips

- **MySQL Connection Error (`Access denied` or `Connection refused`):** Ensure your MySQL service is running locally on port `3306` and that the database name, username, and password in `DATABASE_URL` match your MySQL setup.
- **Resend OTP Error (`403 Forbidden` / `Validation error`):** Ensure `RESEND_FROM_EMAIL` is set to `onboarding@resend.dev` and that the recipient email address matches your Resend account email.
- **Backblaze B2 Presigned URL Issues:** Ensure your application key has read/write permissions for the specific bucket, and verify that `AWS_ENDPOINT_URL` begins with `https://`.
- **CORS Errors:** The backend is preconfigured to permit origins from `http://localhost:5173` and `http://127.0.0.1:5173`. Make sure the frontend is running on port `5173`.
