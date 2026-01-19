# AI Context & Agent Guidelines

This file defines how agentic coding tools (Cursor, Copilot, Windsurf, Claude, CLI agents) should behave in this repository.

The goal is to help engineering teams use AI to maintain the "Modern AI Developer Stack" curated in `README.md`.

---

## 1. Repository Purpose & Layout

- This repo is a documentation-first, Awesome-style list of AI-native engineering tools.
- The primary artifact is `README.md`; there is no app or library at the root today.
- Any code or examples that are added should demonstrate agentic, spec-driven workflows for developers.
- Root docs:
  - `README.md` – curated stack and main content.
  - `AGENTS.md` – this operational guide for agents.
  - `LICENSE.md`, `CONTRIBUTING.md` – standard metadata.
- There is currently no language-specific toolchain at the repo root:
  - No `package.json`, `pyproject.toml`, `Cargo.toml`, or equivalent.
  - No top-level build, lint, or test script is defined.
- If you add a subproject (Node, Python, Rust, etc.), create it in a subdirectory and document it:
  - In the subproject `README`.
  - In a short note in this file under the relevant language section.

---

## 2. Build / Lint / Test Commands

These conventions apply when you introduce code or tooling. Always prefer simple, scriptable commands that work in CI.

### 2.1 Markdown Documentation (current state)

The repo is Markdown-only. Recommended commands once a Node toolchain exists:

```bash
npx markdownlint-cli "**/*.md"           # lint all markdown files
npx markdown-link-check README.md         # check links in the main doc
```

If you wire these into `package.json`, expose canonical scripts:

```jsonc
{
  "scripts": {
    "lint:md": "markdownlint \"**/*.md\"",
    "test:links": "markdown-link-check README.md"
  }
}
```

### 2.2 Node / TypeScript Subprojects

When you add a Node/TypeScript subproject (in a subdirectory):

- Use `npm` or `pnpm` consistently inside that subproject.
- Add at least these scripts in its `package.json`:

```jsonc
{
  "scripts": {
    "build": "tsc -p .",
    "lint": "eslint .",
    "test": "vitest run" // or "jest"
  }
}
```

Testing conventions:

- All tests (Vitest):

```bash
npm test                      # or: pnpm test
```

- Single test file (Vitest):

```bash
npx vitest run path/to/file.test.ts
```

- Single test by name (Vitest):

```bash
npx vitest run --testNamePattern "test name substring"
```

- Single Jest test file:

```bash
npx jest path/to/file.test.ts
```

- Single Jest test by name:

```bash
npx jest -t "test name substring"
```

Prefer Vitest for new projects unless there is a strong reason to use Jest.

### 2.3 Python Subprojects

When you add Python-based tools or examples:

- Use `uv` or `pip` + `venv` for dependency management.
- Default test runner: `pytest`.

Common commands:

```bash
pytest                                   # run all tests
pytest path/test_file.py::test_case      # run single test case
ruff check .                             # lint (if ruff is used)
ruff format .                            # formatting (if ruff-format is used)
```

If you add type checking:

```bash
mypy .                                   # or: pyright
```

### 2.4 Other Languages (if introduced)

If you introduce additional languages, favor standard workflows and document deviations here.

- Go:

```bash
go test ./...                            # all tests
go test ./... -run TestName              # single test
```

- Rust:

```bash
cargo test                               # all tests
cargo test test_name                     # single test
```

For any other language, follow its standard `build`/`lint`/`test` commands and add a short section to this file.

---

## 3. Code Style Guidelines

These rules apply to any code added to this repository unless a subproject documents stricter rules.

### 3.1 Language & Types

- Prefer TypeScript over plain JavaScript for new Node-based code.
- Enable strict typing (`strict: true` in `tsconfig.json`) by default.
- Avoid `any` in TypeScript; use precise types or generics instead.
- For Python, use type hints and run static analysis (`mypy` or `pyright`) when feasible.
- Prefer explicit return types for exported functions and public APIs.

### 3.2 Imports & Modules

- Prefer ES modules (`import` / `export`) in JS/TS projects.
- Group imports with this order and a blank line between groups:
  1) Standard library.
  2) Third-party dependencies.
  3) Internal modules.
- Use path-mapped or short absolute imports instead of deep relative chains.
- Avoid wildcard imports except in test helpers or clearly scoped namespaces.
- Keep side-effect-only imports (`import "./polyfill"`) rare and clearly documented.

### 3.3 Formatting

- Default formatter: Prettier for JS/TS/JSON/Markdown when a Node toolchain exists.
- For Python, prefer `black` or `ruff format` for consistent formatting.
- Use LF line endings and avoid trailing whitespace.
- Target a line length of 100–120 characters.
- Do not rely on manual alignment that an autoformatter will undo.

### 3.4 Naming Conventions

- Variables and functions: `camelCase`.
- Classes, React components, and types/interfaces: `PascalCase`.
- Constants: `SCREAMING_SNAKE_CASE` for global or configuration constants only.
- Files:
  - Runtime modules and scripts: `kebab-case`.
  - React components: `PascalCase`.
  - Tests: mirror the file under test, e.g. `thing.ts` -> `thing.test.ts`.

### 3.5 Error Handling

- Fail fast in tooling and CLIs; do not silently ignore failures.
- Prefer language-standard error types, extending them only when necessary.
- Always provide actionable error messages with context (what failed and what to try next).
- For async JS/TS:
  - Use `try/catch` around the smallest block that can fail.
  - Avoid unhandled promise rejections; `await` or explicitly handle all promises.
- For CLI tools:
  - Exit with non-zero status codes on failure.
  - Print errors to stderr; keep messages concise but specific.

### 3.6 Testing Practices

- Prefer many small, focused tests over a few large integration suites.
- Keep tests deterministic; avoid live network or external API calls unless explicitly mocked.
- Name tests to describe behavior, not implementation details.
- For agent-focused tooling, include examples that can run in CI as part of the test suite.

---

## 4. Cursor & Copilot Rules

- There is currently no `.cursor/rules/` directory and no `.cursorrules` file in this repo.
- There is currently no `.github/copilot-instructions.md` file in this repo.
- If you add Cursor or Copilot instruction files:
  - Ensure their constraints and workflows are consistent with this `AGENTS.md`.
  - Mirror any important rules here so all agents see a single source of truth.
  - Avoid IDE-specific rules that contradict the formatting, naming, or testing conventions above.

---

## 5. Agent Workflow Expectations

- Optimize for reproducible flows over ad hoc chat:
  - Prefer scripts, specs, and small utilities that other agents can invoke.
  - When adding commands, document them clearly in both the code and the relevant README.
- Treat `README.md` as the product:
  - Keep entries focused on developer enablement, not marketing copy.
  - Align new tools with the existing taxonomy in the main README.
- When editing Markdown:
  - Maintain consistent heading levels and bullet styles.
  - Use concise, value-focused descriptions for tools and examples.
- When using git from an agent:
  - Avoid destructive commands (`reset --hard`, force pushes) unless the user explicitly requests them.
  - Do not modify unrelated files or revert user changes.

Agents should always ask: "Does this change help developers build and maintain better software with AI, and is it easy for other agents to follow?"

---

## 6. Using Agent Skills & NotebookLM

- Prefer reusable skills for complex or repeatable workflows:
  - Use the [Agent Skills](https://agentskills.io/home) format (folders with `SKILL.md`, scripts, and assets) when you capture a process that other agents should repeat.
  - Keep skills in version control so changes are reviewable and auditable.
- When designing a skill:
  - Make the skill single-responsibility (one clear capability per skill).
  - Include concrete examples and edge cases in the `SKILL.md` instructions.
  - Document required tools, APIs, and environment assumptions.
- For agents that support skills natively (e.g., Claude with skills, MCP-based agents):
  - Prefer loading skills on demand instead of inlining long instructions into prompts.
  - Share skills across compatible tools instead of duplicating logic per agent.
- When working with external knowledge tools like [NotebookLM](https://notebooklm.google):
  - Treat them as grounded research backends; summarize findings back into this repo.
  - Do not rely on NotebookLM as the only copy of critical instructions; mirror important outputs into `README.md`, `AGENTS.md`, or dedicated docs.

---

## 7. How to Add a New Skill to This Repo

- Location:
  - Place skills under `skills/<skill-name>/SKILL.md`.
  - Keep `skill-name` kebab-case, e.g. `example-summarize-notes`.
- Structure of `SKILL.md`:
  - Start with a title and a one-line purpose statement.
  - Include sections for: inputs, workflow steps, output format, and constraints.
  - Describe how the skill should interact with this repo (files to read/write, where to update docs).
- Design guidelines:
  - Make each skill single-responsibility (one clear capability).
  - Prefer small, composable skills over large, do-everything instructions.
  - Assume other agents may call the skill without prior context; be explicit.
- Versioning:
  - Keep skills under git; document breaking changes in a short `Changelog` section in `SKILL.md`.
  - If a skill becomes obsolete, mark it as deprecated rather than deleting it immediately.
- Discovery:
  - Optionally add a short note in `README.md` or `AGENTS.md` pointing to important skills.
  - Name skills so their purpose is obvious from the folder name.
