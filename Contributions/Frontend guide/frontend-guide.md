# Swan Drive — Frontend Development & UI Design Guide

Welcome to the **Swan Drive Frontend Guide**! We warmly welcome developers and UI designers who want to learn, experiment, and help make Swan Drive visually refined, intuitive, and responsive across all devices.

Whether you specialize in React components, design systems, responsive layouts, or accessibility improvements, this guide outlines the core frontend principles and workflow expectations.

---

## 🎨 Contributor Freedom & Code Style

- **Code in Your Own Style:** Developers are free to write React components, hooks, and helper functions in their own personal coding style. We intentionally do not provide mandatory line-by-line code snippets or force specific syntax patterns.
- **Flexible Fonts & Color Choices:** Frontend developers are welcome to use any fonts and colors (such as black, grey, silver, green, and cohesive accents) that are suitable and elevate our project UI/UX.
- **Document Code Modifications:** After completing frontend code modifications, developers are required to write down what changes were made in a small document inside the `documentation/` folder before submitting their pull request.
- **Maintain Architectural Separation:** We only ask that your implementation respects the project's separation of concerns:
  - Keep presentational markup and view state inside UI components (`frontend/src/components/`, `frontend/src/pages/`).
  - Keep all network requests and Axios calls inside API modules (`frontend/src/apis/`).
  - Keep domain logic, data transformations, and storage interactions inside services (`frontend/src/services/`).
  - Keep shared cross-page state inside React Context (`frontend/src/context/`).

---

## 🌿 Branching Requirement

- **Feature Branches Required:** Always create a dedicated **feature branch** (such as `feature/responsive-sidebar` or `fix/dialog-padding`) for your changes.
- **Never Submit from Main:** Pull requests must originate from your feature branch. Do not work directly on or submit pull requests from `main`.

---

## 🛠️ Local Setup & Dependency Installation

- **Install Dependencies:** Inside the `frontend/` directory, run `npm ci` to install exact pinned dependencies cleanly.
- **Development Server:** Run `npm run dev` to launch the Vite development server.
- **Local Testing Services:** Configure your local environment according to `others/.env.example` using free-tier services (Backblaze B2, Upstash Redis, Qdrant Cloud, local MySQL, and free Gemini API keys) and backend started via `uv sync`.

---

## 🤖 AI Tools, Coding Agents & Playwright MCP

- **AI Tools for Productivity:** Contributors can use any AI assistants, LLMs, or coding agents to improve development and testing speed. Please **use them with caution** by reviewing generated logic, verifying behavior, and ensuring theme tokens are respected.
- **Playwright MCP:** Frontend developers are explicitly encouraged to use **coding agents** and the **Playwright MCP** server for live browser testing, component interaction verification, and testing across responsive screen widths.

---

## 💻 Technology Stack & Requirements

- **Framework:** React 19 powered by Vite.
- **Design & Component Library:** Material UI (MUI 9) (`@mui/material`, `@mui/icons-material`).
- **Language:** Pure **JavaScript (`.jsx` and `.js`)** alone. TypeScript is not required or used, keeping component files lightweight, clean, and free of typing overhead.
- **HTTP Client:** Axios with centralized interceptors.
- **Dependency Discipline:** Use existing dependencies instead of adding external styling or UI packages.

---

## 📐 Frontend Design Principles

When designing or refining UI components for Swan Drive:

1. **Clean, Modern, and Minimal:** Prioritize uncluttered layouts that highlight document workflows and Swan AI interactions.
2. **Mobile and Desktop Responsiveness:** Ensure every dialog, button, drawer, and table adapts gracefully to mobile viewports, tablets, and wide monitors. Ensure touch targets on mobile devices are easy to tap.
3. **Visual Identity, Fonts & Color Palette:**
   - Developers are welcome to use any fonts and colors (such as black, grey, silver, green, and cohesive neutral tones) that look clean, professional, and suitable for Swan Drive's UI/UX.
   - Baseline Theme Accents: The core theme uses Swan Orange (`#FF6B00`) for highlights, logo accents, and selected items, and Water Blue (`#0284C7`) for primary action buttons.
   - Dual Themes: Always verify that your chosen fonts, colors, and contrast combinations look balanced and readable in both Light and Dark modes.
4. **Leverage MUI Capabilities:** Use built-in Material UI components and layout systems. Feel free to incorporate appropriate MUI components (such as loaders, dialogs, badges, or chips) to elevate visual polish.

---

## 🚫 Strict Design Restrictions

To maintain codebase uniformity and avoid styling conflicts:

- **No CSS Frameworks:** Do not introduce Tailwind CSS, Bootstrap, or alternate component libraries.
- **Zero Custom CSS/SCSS:** Never create or import `.css`, `.scss`, or `.module.css` files. Style components exclusively using MUI's `sx` prop, `styled()` utility, and theme design tokens.
- **Preserve Functionality:** Visual refinements must never break or regress existing document management or chat features.

---

## 🧪 UI Verification Checklist

Before submitting your pull request:
1. Verify layouts on mobile (375px–430px), tablet (768px), and desktop viewports (Playwright MCP can assist).
2. Check color contrast and readability in both Light and Dark modes.
3. Verify interactive states (hover, focus, disabled, loading).
4. Ensure no browser console warnings or unhandled errors occur during interaction.
5. Write a small document inside the `documentation/` folder describing what changes were made.
