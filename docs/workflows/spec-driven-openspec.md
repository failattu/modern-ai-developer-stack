# Spec-Driven Development with OpenSpec

This guide shows a concrete, end-to-end example of using Spec-Driven Development (SDD) with OpenSpec and this repository's standards.

Use this flow inside a code subproject (for example `services/api`), not at the repo root.

---

## 1. Initialize OpenSpec in Your Project

Install OpenSpec and initialize it in your service directory:

```bash
npm install -g @fission-ai/openspec
openspec init
```

This will:

- Create an `openspec/` directory with specs and changes folders.
- Generate an `openspec/AGENTS.md` handoff file that AI coding agents can read.

---

## 2. Draft a Change Proposal with Your AI Agent

Ask your AI agent (Claude Code, Codex, Cursor, etc.) to create a change proposal for a small feature. For example, a health-check endpoint:

```text
Create an OpenSpec change proposal for adding a health-check endpoint
that follows the TypeScript web service standards in docs/standards/typescript.md.
```

The agent should scaffold a change folder under `openspec/changes/`, for example:

```text
openspec/
  changes/
    add-health-check/
      proposal.md
      tasks.md
      specs/
        service/
          spec.md   # spec delta
```

- `proposal.md` captures intent and scope.
- `tasks.md` lists concrete implementation tasks.
- `specs/.../spec.md` contains the spec delta describing the new behavior.

---

## 3. Review and Refine the Spec Before Coding

Use the OpenSpec CLI to inspect the change:

```bash
openspec list
openspec show add-health-check
openspec validate add-health-check
```

Iterate with your AI agent until:

- Requirements are clearly stated.
- Scenarios cover happy paths and key edge cases.
- Tasks are specific and traceable to the requirements.

At this stage, you are still not writing production code—only aligning on intent.

---

## 4. Implement Tasks Using This Repo's Standards

Once the spec looks good, ask your AI agent to implement the feature using the agreed tasks and this repository's standards:

```text
The specs for add-health-check are approved. Implement the tasks in
openspec/changes/add-health-check/tasks.md using the layout and scripts
from docs/standards/typescript.md and collections/typescript-web-service.md.
```

The agent should:

- Use the TypeScript layout under your service directory (`src/`, `tests/`).
- Add or update tests under `tests/unit` and/or `tests/integration`.
- Wire scripts like `npm test`, `npm run lint`, and `npm run build` if they are missing.

Run the tests and linters locally:

```bash
npm test
npm run lint
npm run build
```

Fix any issues before proceeding.

---

## 5. Archive the Change and Update Specs

When implementation is complete and tests pass, archive the OpenSpec change so the spec delta becomes part of the source-of-truth specs:

```bash
openspec archive add-health-check --yes
```

This will:

- Merge the approved spec delta into `openspec/specs/.../spec.md`.
- Move the change folder into the archive.

You now have:

- Documented requirements and scenarios.
- A traceable set of tasks that were implemented.
- Code, tests, and specs aligned for this feature.

---

## 6. How AI Agents Should Use This Flow

When working in this repository, AI coding agents should:

1. Check for an existing `openspec/` directory and use it if present.
2. Propose new work as OpenSpec changes (not ad-hoc code edits).
3. Implement tasks only after spec deltas have been reviewed.
4. Archive completed changes so specs stay in sync with the code.

This keeps intent, tasks, and implementation aligned for both humans and AI tools, and makes it easy to audit and evolve your system over time.
