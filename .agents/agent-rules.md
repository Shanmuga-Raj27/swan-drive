# Project Rules & Agent Operating Guidelines

> Strict operational rulebook for AI agents working in this repository. All rules are mandatory and non-negotiable.

---

## 1. Context First (Lookup Before Scanning)
- **Always read `.agents/agent-memory.md` and `.agents/agent-rules.md`** at the beginning of any session or task before searching, listing, or reading source files.
- **Use the Responsibility → File Path Lookup Map** in `agent-memory.md` to navigate directly to the target file.
- **Do not run broad codebase scans**, recursive directory searches, or exploratory grep runs when the lookup map already specifies the responsible files.

---

## 2. Tech Stack & Dependency Discipline
- **Product Name:** **Swan Drive** (strictly two words with space, never "SwanDrive").
- **Strictly adhere to the existing tech stack:**
  - Backend: Python 3.12, FastAPI, SQLAlchemy 2, Alembic, Uvicorn/Gunicorn, PyMuPDF, Google GenAI SDK (`google-genai`).
  - Frontend: React 19, Vite, Material UI (`@mui/material`), Axios.
  - Databases & Cloud: AWS RDS MySQL 8, Qdrant Cloud, Upstash Redis (REST), AWS S3 / Backblaze B2.
- **Never install new packages, libraries, or dependencies** on your own (no `pip install`, `uv add`, `npm install`, `yarn add`, etc.).
- **Always request explicit user approval** before proposing or adding any dependency, and only recommend new tools if genuinely essential to the project.

---

## 3. Explicit Permission for Critical Operations
Always ask clarifying questions and request explicit user confirmation before:
1. **Deleting files, directories, or substantial code blocks.**
2. **Modifying database schemas**, creating or running new Alembic migrations, or altering ORM models in `backend/app/database/db_models.py`.
3. **Changing core architecture**, request flows, security/auth patterns, or data pipelines.
4. **Performing multi-file refactors** across backend services or frontend pages.

---

## 4. Shell, Git & MCP Restrictions
- **Authorized shell execution:** The agent may use essential shell/bash commands only if the task requires it (e.g., inspecting files, exact line counts, directory checks, or running targeted test scripts explicitly requested by the user). Never run heavy build commands, docker commands, or background daemons without explicit user direction or permission granted in conversation.
- **Git read commands permitted:** The agent may use `git diff` and other read-only inspection commands (`git status`, `git log`) to verify code changes and track modifications. Strictly never run git write commands (`git commit`, `git push`, `git reset`, `git checkout`, `git rebase`, `git clean`) automatically.
- **No automatic test suites:** Never launch full automated test suites (e.g., `pytest`, `npm test`, `vitest`) automatically unless explicitly requested by the user.
- **No unapproved MCP tools:** Never call external MCP servers or tools unless specifically asked by the user.

---

## 5. Fast Execution for Small Frontend Tasks
- Execute small frontend UI tasks (tweaking copy, updating icons, adjusting component props, modifying layout alignment) **quickly and directly**.
- Do not run unnecessary deep exploratory codebase scans, test runs, or heavy architectural over-engineering for simple UI adjustments.

---

## 6. Frontend Architecture & Styling (React MUI Only)
- **Strictly React MUI:** All components and styling must exclusively use `@mui/material`, `@mui/icons-material`, the `sx` prop, and theme tokens defined in `frontend/src/theme.js`.
- **Zero CSS/SCSS files:** **NEVER** create, edit, or import `.css`, `.scss`, or `.module.css` stylesheets. Never introduce Tailwind, Bootstrap, Emotion styled-components, or external CSS frameworks.
- **Strict Separation of Concerns:**
  - UI components (`src/components/`, `src/pages/`) must strictly handle presentation and local view state.
  - **Never place API calls, direct Axios invocations, or data-fetching logic inside UI components.**
  - All network communication belongs in `src/apis/` (e.g., `documentApi.js`, `ragApi.js`, `authApi.js`).
  - All business logic, selection rules, formatting, and data transformation belong in `src/services/` (e.g., `ragService.js`, `vaultSelectionService.js`, `authService.js`).
  - Shared cross-page state belongs in dedicated contexts (e.g., `KnowledgeBaseFilesContext.jsx`).

---

## 7. Strict Secrets & Privacy Protection
- **NEVER read, open, cat, parse, log, or output:**
  - `.env`, `.env.*`, or any environment file in `others/` or anywhere in the workspace.
  - Credential files, CSV export files (e.g., `aws-secret/*.csv`), PEM private keys (`*.pem`), or bearer tokens.
- If environment variable configuration is needed, reference variable names exclusively from `others/.env.example` or `backend/app/core/config.py`.
- **Device & Personal Privacy Rule:** Never include personal usernames (e.g., device username), user home directory paths, machine-specific paths, or system credentials in documentation, memory files, or file lookup maps. Always use repository-relative paths starting strictly from the project's root folder names (e.g., `backend/...`, `frontend/...`, `others/...`).
- **Product Branding:** Always format the brand name as **Swan Drive** (two words, capitalized). Never use "SwanDrive".
- **Public Documentation Rule:** Public documentation must never contain secrets, credentials, personal usernames, or machine-specific file paths. Use safe placeholders instead.
