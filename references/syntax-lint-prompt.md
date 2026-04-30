# Syntax lint prompt

A self-contained prompt for catching parser-blocking syntax footguns in a Mermaid diagram before it reaches the user. Paste this file as context, paste the Mermaid source beneath it, and Claude returns a JSON list of `(find, replace)` fixes to apply verbatim.

Used by SKILL.md step 6 alongside the panel critique. The linter is mechanical: it does not assess design, structure, or semantics — that is the panel's job. The linter only catches errors that block GitHub or Mermaid v11 from rendering.

---

## How to use this file

1. Paste everything below the `--- BEGIN PROMPT ---` marker into a fresh Claude Code session.
2. Beneath the prompt, paste the Mermaid source (just the source — no markdown fences, no plan, no surrounding context).
3. Read the JSON list Claude returns.
4. Apply each `auto_fixes` entry's `(find, replace)` verbatim to the source. Surface any `manual_fixes` to whoever produced the diagram.

If the linter returns `all_clear: true`, no fixes are needed.

---

--- BEGIN PROMPT ---

You are running a syntax linter against a Mermaid diagram source. Your job is to catch parser-blocking footguns that cause GitHub or Mermaid v11 to fail rendering. You are NOT critiquing diagram design, semantics, color choices, or any property a panel critique would handle — those are out of scope for the linter.

Scan the source for the following footgun patterns. For each pattern that fires, emit an `auto_fixes` entry (if the fix is mechanical) or a `manual_fixes` entry (if the fix requires renaming or restructuring).

## Footguns to catch

**1. Unquoted special characters in edge labels.** Edge labels appear between `|...|` after an arrow (`-->`, `-.->`, `<-->`, `==>`, etc.). Labels containing any of `(`, `)`, `[`, `]`, `{`, `}`, `@`, `>`, or unescaped `"` must be wrapped in double quotes. Examples:

- `norm -.->|products[]| resolver` → unquoted `[]` → fails. Fix: `norm -.->|"products[]"| resolver`.
- `A -->|cost > 0.8| B` → unquoted `>` → fails. Fix: `A -->|"cost > 0.8"| B`.
- `gw -->|"redacted prompt + tenant_id"| plan` → already quoted, leave alone.

**2. Unquoted `@` or other special chars in node labels.** Inside non-basic node shapes (cylinders `[(...)]`, decisions `{...}`, pills `([...])`, stadium shapes), `@` triggers Mermaid v11's edge-naming syntax. Examples:

- `metrics[(Metrics<br/>recall@k)]` → unquoted `@` → fails on v11. Fix: `metrics[("Metrics<br/>recall@k")]`.

(Basic `[label]` shape is more permissive but quoting is always safe.)

**3. Subgraph display name without quotes.** Format must be `subgraph id ["Display Name"]`:

- `subgraph my platform` → fails on the space. Fix: `subgraph my_platform [" My Platform "]`.
- `subgraph TENANT [" Tenant-scoped "]` → already correct, leave alone.

**4. Reserved keywords as node IDs.** `end`, `class`, `style`, `subgraph`, `direction` cannot be used as node identifiers — they're either parser keywords or directive starters. This is a `manual_fix` because resolution requires renaming all references:

- `end[Done]` → `end` is reserved → emit a manual fix recommending `end_node[Done]`.

**5. Markdown-list trap in node labels.** A label starting with `N. ` (digit, period, space) inside a basic `[...]` shape parses as a numbered list ordinal:

- `step1[1. First step]` → fails. Fix: `step1["1. First step"]` (quote it).

**6. linkStyle index out of range.** `linkStyle N stroke:...` with `N` greater than the number of edges declared above it. Count edges (any line containing `-->`, `-.->`, `<-->`, `==>`); if any `linkStyle` references an index ≥ that count, emit a manual fix.

## What NOT to flag

- Borderline judgment calls (verbose labels, unusual color choices, layout preferences) — those are the panel's job.
- Stylistic inconsistency (some labels quoted, some not, when both forms parse) — only flag the unparseable ones.
- Missing or extra whitespace inside subgraph headers — Mermaid is forgiving here.
- Anything that renders correctly even if it's awkward — the linter's mandate is "does it parse," not "is it beautiful."

## Output format

Return a single JSON object, no prose before or after, no markdown code fence around the JSON:

```json
{
  "all_clear": false,
  "auto_fixes": [
    {
      "category": "unquoted-special-chars-in-edge-label",
      "find": "norm -.->|products[]| resolver",
      "replace": "norm -.->|\"products[]\"| resolver",
      "explanation": "Square brackets in unquoted edge label trigger a v11 parse error (got 'SQS')."
    }
  ],
  "manual_fixes": []
}
```

Field semantics:

- `all_clear`: `true` only if no footguns were found.
- `auto_fixes`: each entry has `category`, `find` (the exact source substring to locate — must be unique in the source, include enough context if needed), `replace` (the corrected version), and `explanation` (one sentence, including the parser error if known).
- `manual_fixes`: each entry has `category`, `line_excerpt`, and `explanation`. These cannot be auto-applied — the diagram needs human intervention.

If the source is syntax-clean:

```json
{"all_clear": true, "auto_fixes": [], "manual_fixes": []}
```

--- END PROMPT ---
