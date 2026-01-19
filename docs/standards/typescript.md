# TypeScript Service Standards

Opinionated guidelines for building TypeScript/Node services that fit the "Modern AI Developer Stack" in this repo. These apply to new subprojects created under their own directory (not at the repo root).

---

## 1. Project Layout

- Create a subdirectory per service, for example `services/api`.
- Use this basic layout:

```text
services/my-service/
  package.json
  tsconfig.json
  src/
    index.ts
    lib/
  tests/
    unit/
    integration/
```

- Keep runtime code under `src/` and tests under `tests/`.
- Avoid putting source files alongside configuration files in the project root.

---

## 2. Tooling & Scripts

- Use `npm` or `pnpm` consistently inside the subproject.
- Recommended `package.json` scripts:

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

- Prefer Vitest for new projects; use Jest only if you have a strong reason.

---

## 3. TypeScript Configuration

- Create `tsconfig.json` with `strict` mode enabled:

```jsonc
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "outDir": "dist",
    "rootDir": "src",
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true
  },
  "include": ["src", "tests"]
}
```

- Prefer explicit return types on exported functions and public APIs.
- Avoid `any` unless you can clearly justify it in a small, isolated place.

---

## 4. Linting & Formatting

- Use ESLint with the TypeScript plugin and Prettier for formatting.
- Example minimal `.eslintrc.cjs`:

```js
module.exports = {
  root: true,
  parser: "@typescript-eslint/parser",
  plugins: ["@typescript-eslint"],
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/recommended-requiring-type-checking",
    "prettier"
  ],
  parserOptions: {
    project: ["./tsconfig.json"]
  },
  env: {
    node: true,
    es2020: true
  }
};
```

- Run `npm run lint` and `npm run format` in CI.
- Keep line length in the 100–120 character range; let Prettier manage wrapping.

---

## 5. Imports & Modules

- Use ES modules (`import` / `export`) instead of CommonJS where possible.
- Group imports in this order with blank lines between groups:
  1. Node standard library.
  2. Third-party packages.
  3. Internal modules.

```ts
import fs from "node:fs";

import express from "express";

import { createAppConfig } from "./config";
```

- Prefer path-mapped or short absolute imports over deep relative chains when the project grows.
- Avoid wildcard imports except in test helpers or intentionally scoped namespaces.

---

## 6. Testing Practices

- Place unit tests under `tests/unit` and integration tests under `tests/integration`.
- Name tests after behavior, not implementation details.
- Prefer many small, focused tests.
- Use Vitest snapshots sparingly; favor explicit assertions.

**Common commands (Vitest):**

```bash
npm test                                   # run all tests
npx vitest run tests/unit/user.test.ts     # single file
npx vitest run --testNamePattern "creates user"  # single test
```

---

## 7. Error Handling

- Fail fast in CLIs and tooling; do not silently ignore errors.
- Use built-in error types or small custom classes where necessary:

```ts
export class ConfigError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "ConfigError";
  }
}
```

- Include enough context in error messages to debug in logs.
- For async code:
  - Use `try/catch` around the minimal block that can fail.
  - Avoid unhandled promise rejections; always `await` or handle promises.

---

## 8. Command Cheat Sheet

- `npm run build` – compile TypeScript to JavaScript.
- `npm test` – run the full test suite with Vitest.
- `npm run test:unit` – run only unit tests.
- `npm run test:integration` – run only integration tests.
- `npm run lint` – run ESLint with TypeScript rules.
- `npm run format` – format code with Prettier.
