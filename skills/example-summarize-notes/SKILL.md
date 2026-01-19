# Example Skill: Summarize Notes with NotebookLM

## Skill Purpose

Help an AI agent take a collection of notes (Markdown, text, transcripts) in this repo and produce a concise, grounded summary that can be safely added back to `README.md` or `AGENTS.md`.

## When to Use This Skill

- You have 2–20 small notes, docs, or transcripts that all relate to the same topic.
- You want a short summary (1–3 paragraphs or 3–7 bullets) capturing key ideas for developers.
- You want the summary to be grounded in the source material, not hallucinated.

## Inputs

- `note_paths`: list of file paths (relative to repo root) to summarize.
- `summary_style` (optional): one of `bullets`, `paragraph`, or `mixed`.
- `target_audience` (optional): short description, e.g. `backend engineers`, `ML platform team`.

## High-Level Workflow

1. Collect the contents of all `note_paths`.
2. Optionally use an external grounding tool like NotebookLM for deeper analysis.
3. Ask the model to propose a candidate summary.
4. Cross-check the summary against the source notes; remove unsupported claims.
5. Emit a final summary plus a short "How to integrate into this repo" suggestion.

## Detailed Agent Instructions

Follow these steps whenever this skill is invoked:

1. **Load Inputs**
   - Read all files listed in `note_paths`.
   - If any file is missing or empty, report which ones and continue with the rest.

2. **Normalize Content**
   - Strip boilerplate (license headers, navigation, TOCs) where obvious.
   - Preserve code snippets and CLI commands exactly; do not rewrite them.

3. **Grounded Analysis (Optional NotebookLM Step)**
   - If NotebookLM is available:
     - Create or update a NotebookLM notebook with the note contents.
     - Ask NotebookLM for 3–5 key takeaways and 3 concrete examples or use cases.
     - Use those responses as additional context, but do not copy phrasing verbatim.
   - If NotebookLM is not available, skip this step and rely on local analysis.

4. **Draft Summary**
   - If `summary_style` is `bullets`, produce 3–7 bullets.
   - If `summary_style` is `paragraph`, produce 1–3 short paragraphs.
   - If `summary_style` is `mixed` or not provided, use 1 short paragraph plus 3–5 bullets.
   - Target audience:
     - If `target_audience` is provided, emphasize what matters for that audience.
     - Otherwise, assume "software engineers adopting AI tooling".

5. **Grounding Check**
   - For each claim in the summary, ensure you can point to at least one source sentence or snippet.
   - Remove or soften any claim that cannot be grounded (e.g., "may", "can often").

6. **Repo Integration Suggestion**
   - Add a short section titled `How to use this summary in this repo` with 2–3 bullets, such as:
     - Where in `README.md` it could fit.
     - Whether it belongs in `AGENTS.md` or a new doc.
     - Any follow-up tasks (e.g., add a tool entry, refine taxonomy).

## Output Format

Respond with a single Markdown block using this structure:

```markdown
## Summary

<summary text here>

## How to use this summary in this repo

- <bullet 1>
- <bullet 2>
- <bullet 3>
```

Do not include raw source content in the output unless explicitly requested; link to files instead.

## Constraints

- Keep summaries under 250 words unless the user explicitly asks for more.
- Do not fabricate tools, products, or capabilities; rely on the provided notes.
- Maintain neutral, technical tone; avoid marketing language.

## Example Invocation (Pseudocode)

```jsonc
{
  "skill": "example-summarize-notes",
  "args": {
    "note_paths": [
      "notes/notebooklm-usage.md",
      "notes/agent-skills-patterns.md"
    ],
    "summary_style": "mixed",
    "target_audience": "platform engineers evaluating AI tooling"
  }
}
```