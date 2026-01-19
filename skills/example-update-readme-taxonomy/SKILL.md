# Example Skill: Update README Taxonomy

## Skill Purpose

Help an AI agent safely update the taxonomy and tool listings in `README.md` while preserving structure, style, and consistency with this repository's goals.

## When to Use This Skill

- You want to add, move, or remove tools from the main stack.
- You need to introduce a new category or rename an existing one.
- You are cleaning up descriptions to stay aligned with developer-focused, non-marketing language.

## Inputs

- `change_intent`: short description of what should change (e.g. "add NotebookLM to Enterprise Knowledge").
- `proposed_entries` (optional): list of proposed Markdown bullets for new or updated tools.
- `target_section` (optional): name of the target section or subsection in `README.md`.
- `constraints` (optional): extra rules, e.g. "do not rename existing categories".

## High-Level Workflow

1. Inspect the current taxonomy and relevant sections in `README.md`.
2. Build a clear, minimal change plan that matches `change_intent`.
3. Apply edits in a way that preserves structure, style, and ordering.
4. Re-scan the file to confirm consistency and formatting.

## Detailed Agent Instructions

When this skill is invoked, follow these steps:

1. **Load README**
   - Read `README.md` from the repo root.
   - Identify top-level sections (e.g. "Core Development", "Architecture", "Building & Orchestrating Agents").

2. **Understand the Intent**
   - Parse `change_intent` into one or more concrete actions, such as:
     - Add a new tool entry.
     - Update the description of an existing tool.
     - Move a tool between categories.
     - Rename or add a category.
   - If `proposed_entries` is provided, treat them as suggestions; adjust wording to fit house style.

3. **Locate Target Section**
   - If `target_section` is provided, match it to a heading in `README.md` (case-insensitive).
   - If not provided, infer the best section based on the tool's purpose using the existing taxonomy.
   - If no reasonable match exists, consider whether a new subsection is warranted and include that in your plan.

4. **Draft Change Plan (Internal)**
   - Before editing, build a short internal plan that answers:
     - Which lines or bullets will be touched.
     - Whether any category headings will be added or renamed.
     - How the new or updated entries will be ordered relative to existing items.
   - Prefer minimal, targeted edits over broad reorganization.

5. **Apply Edits**
   - Maintain the existing heading hierarchy and emoji usage.
   - For new tool entries, use this format:
     - `* **[Tool Name](URL)** - Brief, value-focused description.`
   - Keep descriptions concise and specific:
     - Focus on what the tool does for developers.
     - Avoid marketing language and vague claims.
   - When moving tools between sections, remove the original bullet and re-insert it in the new location.

6. **Consistency & Style Check**
   - After editing, re-scan `README.md` for:
     - Consistent bullet formatting and spacing.
     - Correct links and Markdown syntax.
     - Category names that still make sense and match references in other docs (if any).
   - Ensure the file still reads as a coherent "Modern AI Developer Stack" overview.

7. **Summarize Changes**
   - Produce a brief summary suitable for a commit message and PR description, including:
     - Which tools were added/updated/moved.
     - Any new categories and why they were introduced.

## Output Format

Return a Markdown block with:

```markdown
## Planned changes

- <one-line summary of change 1>
- <one-line summary of change 2>

## Applied edits (high level)

- <what you actually changed in README.md>

## Suggested commit message

<single-line commit message>
```

Do not dump the entire `README.md`; only describe the changes and their rationale.

## Constraints

- Do not reorder sections unless explicitly requested.
- Do not remove existing tools unless `change_intent` clearly calls for deprecation or removal.
- Keep new descriptions neutral, technical, and focused on developer value.
- Preserve emojis and overall visual structure unless there is a strong reason to change them.

## Example Invocation (Pseudocode)

```jsonc
{
  "skill": "example-update-readme-taxonomy",
  "args": {
    "change_intent": "add Agent Skills and NotebookLM to the appropriate sections",
    "proposed_entries": [
      "* **[Agent Skills](https://agentskills.io/home)** - An open format for packaging reusable agent skills as folders of instructions, scripts, and resources.",
      "* **[NotebookLM](https://notebooklm.google)** - Google’s AI research and thinking partner that builds grounded assistants over your documents, transcripts, and notes."
    ]
  }
}
```