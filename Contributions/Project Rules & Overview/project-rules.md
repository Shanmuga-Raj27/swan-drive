# Swan Drive — Contributor Rules & Guidelines

Thank you for your interest in contributing to **Swan Drive**! Whether you are submitting a bug fix, improving the user interface, enhancing documentation, or proposing an architectural improvement, we appreciate your effort.

To ensure that the codebase remains clean, maintainable, and reliable for all learners and contributors, please follow these guidelines.

---

## 🌿 Mandatory Branching Rule: Feature Branches Only

- **Feature Branch Required:** Contributors are strictly required to create and work within a dedicated **feature branch** (such as `feature/your-feature-name` or `fix/your-fix-name`).
- **Never Use Main:** Never commit directly to or submit pull requests directly from the `main` branch. Pull requests must originate from your feature branch into Swan Drive's `main` branch.

---

## 🛠️ Local Development & Setup

Swan Drive can be run locally at **zero cost** using free developer tiers (no credit card required).

For step-by-step instructions, refer to the dedicated **[Local Setup Guide](./local-setup-guide.md)**.

---

## 🎨 Developer Freedom & Code Style

- **Your Own Code Style:** Developers are encouraged to write code in their own personal style. Swan Drive does not dictate rigid line-by-line coding syntax or provide mandatory boilerplate code blocks.
- **Core Architecture Boundaries:** We ask only that contributors respect the primary architectural boundaries:
  - **JavaScript Only:** All frontend components, services, and utilities must be written in pure **JavaScript (`.jsx` and `.js`)**. TypeScript is not used.
  - **Material UI Only:** Use Material UI (MUI 9) components and theme tokens for UI development.
  - **Zero Custom CSS/SCSS:** Do not introduce `.css`, `.scss`, Tailwind, or Bootstrap stylesheets.
  - **Separation of Concerns:** Keep API communication in `frontend/src/apis/`, business logic in `frontend/src/services/`, and presentational views in `frontend/src/components/` and `frontend/src/pages/`.
  - **Dependency Discipline:** Leverage existing dependencies rather than introducing new third-party packages.
- **Document Code Modifications:** After making code modifications, developers are required to write down what changes were made in a small document inside the `documentation/` folder.

---

## 🤖 AI Tools & Coding Agents

- **Productivity with Caution:** All contributors are welcome to use any AI tools, large language models (LLMs), or AI coding assistants to improve productivity during development and testing. However, **use them with caution**:
  - Always review and understand generated logic before committing.
  - Ensure generated code complies with project conventions (MUI, separation of concerns).
  - Do not introduce unverified dependencies or commit secrets.
- **Coding Agents & Playwright MCP:** Frontend developers are explicitly authorized to use **coding agents** and the **Playwright MCP** server for automated UI testing, component verification, and responsive viewport checks.

---

## 🔒 Security & Privacy Rules

- **Never Commit Secrets:** Do not commit credentials, API keys, database passwords, JWT tokens, or personal `.env` files.
- **Public Documentation:** Ensure all public documentation, examples, and issues use safe placeholders (such as `YOUR_API_KEY`) and omit private machine file paths or personal usernames.
- **Multi-Tenant Scoping:** Ensure that database queries and vector filters are always strictly scoped to the authenticated user ID.

---

## 📬 Pull Request Checklist

Before submitting your pull request:
1. Confirm changes are on a descriptive **feature branch** (not `main`).
2. Verify that dependencies were installed via `uv sync` (backend) or `npm ci` (frontend).
3. Test locally using the free-tier service configurations outlined above.
4. Ensure your pull request description explains the problem solved and the approach taken.
5. Include before-and-after screenshots for any user-facing UI modifications.
6. Write a small document inside the `documentation/` folder summarizing what changes were made.
