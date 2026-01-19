# Collection: TypeScript Web Service Stack

A practical, opinionated stack for building a TypeScript/Node web service that aligns with the "Modern AI Developer Stack" in this repository.

Use this collection when you want a production-grade service with clear standards for typing, testing, formatting, and AI-assisted workflows.

---

## 1. When to Use This Collection

- You are building a new HTTP API or background worker in TypeScript.
- You want strict typing, fast tests, and easy CI integration.
- You plan to use AI tools (Copilot, Cursor, terminal agents) and want them to follow consistent rules.

If your project is not TypeScript-based, see the Python standards and future collections for other stacks.

---

## 2. Recommended Stack

- Editor / agents:
  - GitHub Copilot or similar IDE assistant.
  - Cursor / Windsurf / agentic IDE configured to read `AGENTS.md`.
- Runtime and tooling:
  - Node.js (LTS).
  - TypeScript with `strict: true` enabled.
  - Vitest for testing (unit and integration).
  - ESLint + Prettier for linting and formatting.
- Observability and quality (optional but recommended):
  - Prompt testing and evaluation tools for AI-heavy services.
  - Logging and tracing suited to your runtime environment.

See `docs/standards/typescript.md` for detailed expectations.

---

## 3. Day-1 Setup Checklist

Inside a new subdirectory such as `services/api`:

1. Initialize the project:

   ```bash
   npm init -y
   npm install typescript vitest @vitest/ui @types/node --save-dev
   npm install eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier eslint-config-prettier --save-dev
   ```

2. Add `tsconfig.json` and `package.json` scripts following `docs/standards/typescript.md`.

3. Create the basic layout:

   ```text
   services/api/
     package.json
     tsconfig.json
     src/
       index.ts
     tests/
       unit/
       integration/
   ```

4. Add `.eslintrc.cjs` and Prettier config.

5. Wire a minimal `npm test` that runs Vitest and confirm it passes in CI.

---

## 4. Project Layout (Reference)

Use this as a reference layout, adjusting names to fit your domain:

```text
services/api/
  package.json
  tsconfig.json
  .eslintrc.cjs
  src/
    index.ts
    config/
      index.ts
    routes/
      health.ts
    lib/
  tests/
    unit/
      health.test.ts
    integration/
      health.integration.test.ts
```

- Keep runtime and domain logic in `src/`.
- Keep tests in `tests/`, not mixed into `src/`.
- As the service grows, prefer more small modules over a few large files.

---

## 5. Scripts & Commands

Recommended `package.json` scripts:

```jsonc
{
  "scripts": {
    "build": "tsc -p .",
    "lint": "eslint .",
    "test": "vitest run",
    "test:watch": "vitest",
    "test:unit": "vitest run tests/unit",
    "test:integration": "vitest run tests/integration",
    "format": "prettier --write ."
  }
}
```

Common commands:

```bash
npm run build                 # compile TypeScript
npm test                      # run full test suite
npm run test:unit             # run unit tests
npm run test:integration      # run integration tests
npm run lint                  # run ESLint
npm run format                # format code with Prettier
```

---

## 6. CI Outline

For GitHub Actions, create a workflow that:

- Runs on pushes and pull requests.
- Installs dependencies with `npm ci`.
- Runs `npm run lint`, `npm test`, and optionally `npm run build`.

Outline:

```yaml
name: ci

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "lts/*"
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

Adjust paths if your service lives in a subdirectory (for example by adding `working-directory` to the steps).

---

## 7. Using AI Tools with This Stack

- Point AI coding agents to `AGENTS.md` so they understand repository-wide expectations.
- When asking an agent to create a new TypeScript service:
  - Specify that it should follow `docs/standards/typescript.md` and this collection.
  - Ask it to:
    - Create the layout shown above under a new subdirectory.
    - Add scripts and configs exactly as described.
    - Wire basic tests (for example, a health check endpoint).

This keeps human and AI contributors aligned on structure, tooling, and quality from the beginning.
