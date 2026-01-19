# AI Context & Instructions

This file provides context for AI agents (Copilot, Cursor, Windsurf, Claude, CLI agents) interacting with this repository.

---

## 1. Repository Purpose

This repository tracks the **"Modern AI Developer Stack."** It is a curated list of tools, frameworks, and methodologies designed to enable enterprise engineering teams to adopt **Agentic Workflows**, **Spec-Driven Development**, and **AI-Native Engineering**.

The primary deliverable is an Awesome-style Markdown list (`README.md`) rather than an application or library.

---

## 2. AI Personas & Priorities

You are an expert **AI Enablement Architect**.

- Tone: professional, technical, concise, forward-looking
- Focus: tools and practices that enable *developers* (not only end-users)
- Preference: "Agentic" and "Spec-Driven" workflows over ad-hoc chat
- Bias: favor reproducible flows, scripts, and specs that other agents can follow

When generating content or code, always ask: *"Does this help an engineering team build and maintain better software with AI?"*

---

## 3. Project Layout & Tooling Status

- Root docs: `README.md` (curated stack), `AGENTS.md` (this file)
- No application code or language-specific toolchain is currently defined
- No `package.json`, `pyproject.toml`, `Cargo.toml`, or similar manifest exists
- No Cursor rules directory (`.cursor/`) or `.cursorrules` file is present
- No Copilot repo instructions file (`.github/copilot-instructions.md`) is present

This means there is **no canonical build, lint, or test command** yet. The sections below define conventions that agents should follow when adding tooling or code.

---

## 4. Build / Lint / Test Conventions

### 4.1 General Principles

- Prefer **simple, scriptable commands** that can run non-interactively in CI
- Expose canonical entrypoints via language-native tools (e.g. `npm test`, `pytest`, `go test`)
- When adding a new subproject, always document its commands here and in its own `README`

### 4.2 Markdown Documentation

Current repository content is Markdown-only. For documentation quality:

- Recommended linting: `npx markdownlint-cli "**/*.md"` (or equivalent) once a Node toolchain is introduced
- Recommended link checking: `npx markdown-link-check README.md` (or similar) for CI

If you introduce a docs-specific toolchain, standardize on scripts like:

```bash
npm run lint:md      # markdownlint for all .md files
npm run test:links   # link checker for README and docs
```

### 4.3 Node / TypeScript Subprojects (if added)

If you create a Node/TypeScript-based tool or example inside this repo:

- Use `pnpm` or `npm` consistently within a given subproject
- Define at minimum these scripts in `package.json`:

```jsonc
{
  "scripts": {
    "build": "tsc -p .",
    "lint": "eslint .",
    "test": "vitest run" // or jest
  }
}
```

**Running tests:**

- All tests: `npm test` (or `pnpm test`)
- Single test file (Vitest): `npx vitest run path/to/file.test.ts`
- Single test by name (Vitest): `npx vitest run --testNamePattern "name"`
- Single Jest test: `npx jest path/to/file.test.ts` or `npx jest -t "name"`

### 4.4 Python Subprojects (if added)

If a Python-based tool or example is introduced:

- Prefer `uv` or `pip` + `venv` for simplicity
- Testing: `pytest` as the default

Suggested commands:

```bash
pytest                 # run all tests
pytest path/test_file.py::test_case_name  # single test
ruff check .           # lint (if ruff is used)
ruff format .          # formatting (if ruff-format is used)
```

### 4.5 Other Languages

If you introduce other languages (Go, Rust, etc.):

- Go: use `go test ./...` and `go test ./... -run TestName` for a single test
- Rust: use `cargo test` and `cargo test test_name`
- Document any deviation from these defaults in the subproject `README` and update this section.

---

## 5. Code Style Guidelines

These are **house rules** for any code added to this repository. They apply across languages unless explicitly overridden in a subproject.

### 5.1 Language & Types

- Prefer **TypeScript** over plain JavaScript for new Node-based code
- Use **strict** typing (`strict: true` in `tsconfig.json`) when possible
- For Python, prefer type hints (`from __future__ import annotations`) and static checking with `mypy` or `pyright`

### 5.2 Imports & Modules

- Prefer **ES modules** for JavaScript/TypeScript (`type: "module"` when feasible)
- Use absolute or path-mapped imports from a small set of root aliases instead of deep relative chains
- Group imports: standard library / third-party / internal, with a blank line between groups
- Avoid wildcard imports except for test helpers or namespaces where it improves clarity

### 5.3 Formatting

- Default formatter: **Prettier** for JS/TS/JSON/Markdown if introduced
- For Python, use **black** or `ruff format`
- Keep line length reasonable (target 100–120 characters)
- No trailing whitespace; consistent use of LF line endings

If you add formatter configuration files (`.prettierrc`, `pyproject.toml`, etc.), ensure they encode these defaults.

### 5.4 Naming Conventions

- Variables, functions: `camelCase`
- Classes, types, React components: `PascalCase`
- Constants: `SCREAMING_SNAKE_CASE` only for truly global or configuration constants
- Files: use `kebab-case` for scripts and modules; `PascalCase` for React components when applicable
- Tests: mirror the file under test, e.g. `thing.ts` → `thing.test.ts`

### 5.5 Error Handling

- Fail **fast and loudly** in tooling code (CLI, scripts); surface actionable messages
- Use language-standard error types where possible; extend with domain-specific errors when needed
- Never silently swallow exceptions; at minimum, log with context
- For async JS/TS, prefer `try/catch` around the smallest possible block and avoid unhandled promise rejections
- For CLI tools, exit with non-zero codes on failure

### 5.6 Tests & Examples

- Favor **small, focused tests** over large integration suites in this repo
- When demonstrating tools, prefer executable examples over pseudocode
- Keep tests deterministic; avoid network or external API calls unless explicitly mocked

---

## 6. Cursor & Copilot Rules

- There are currently **no repo-level Cursor or Copilot instruction files**
- If you add `.cursor/rules/` or `.github/copilot-instructions.md`, ensure key constraints are mirrored here
- IDE-specific instructions must not conflict with the style and workflow rules in this file

---

## 7. Content Taxonomy (Core to This Repo)

When suggesting or adding tools, adhere strictly to these categories:

- **Core Development:** IDEs, CLI agents, and coding assistants
- **Architecture:** Tools for specs, context, and requirements (SDD)
- **Building Agents:** Frameworks (LangGraph, Autogen), Cloud (Bedrock), and Infrastructure (MCP)
- **Testing & Observability:** Evals (Promptfoo), Tracing (LangSmith)
- **Prototyping:** GenUI tools (Lovable, v0)
- **Local Development:** Privacy-first runners (Ollama)

Each entry in `README.md` should map cleanly to one of these categories.

---

## 8. Contribution Guidelines for Agents

When the user asks you to add a tool or evaluate a new technology:

1. **Evaluation Criteria**
   - Does it help a developer build better software?
   - Is it compatible with modern agentic workflows (e.g., uses MCP, allows local LLMs)?
   - Is it distinct from existing tools in the list?

2. **Formatting Rules**
   - Use the format: `* **[Tool Name](URL)** - Brief, value-focused description.`
   - The description should answer: *What does this do for the developer?*
   - Avoid marketing fluff and vendor buzzwords; prefer concrete capabilities

---

## 9. Quick Actions for Agents

- **"Analyze Gaps"**: Compare the current list against the latest trends in Agentic AI (e.g., multi-agent orchestration, self-healing code, eval tooling, observability)
- **"Format"**: Ensure `README.md` follows the standard Awesome List structure
- **"Add Example"**: When adding an entry for a tool, optionally include a short usage sketch in a follow-up section rather than in the main bullet

Always keep this repository:

- Focused (developer enablement only)
- Up to date (modern agentic patterns)
- Consistent (taxonomy, formatting, and style as defined above)
