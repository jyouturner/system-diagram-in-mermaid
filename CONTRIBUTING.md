# Contributing

Thanks for taking a look. This is a small, opinionated skill, and the most useful contributions are concrete examples of where it works and where it doesn't.

## What's most useful

In rough order of value:

1. **Diagrams the skill produces that look wrong.** Open an issue with the prompt you used, the output you got, and a one-sentence statement of what's off. This is the highest-leverage feedback because it tells us where the procedure is failing in practice. A screenshot of the rendered Mermaid is fine; the source is better.
2. **Diagram archetypes the skill mishandles.** "I asked for X-style diagram and the skill insisted on a request trace" — that's useful even without a fix. The skill is biased toward request traces by design, and we want to know when that bias hurts.
3. **Test prompts for `evals/`.** New prompts that exercise underrepresented archetypes (batch pipelines, hardware/firmware diagrams, ML training loops, anything that isn't a web request flow). Include a short note on what the diagram should and shouldn't contain.
4. **Refinements to the diagnostic checklist or pattern reference.** Specific failure modes the checklist misses, or Mermaid idioms the patterns reference doesn't cover.

## What's less useful right now

- Style/wording tweaks to SKILL.md prose unless they fix a real ambiguity.
- New Mermaid features that aren't load-bearing for the procedure. The skill is opinionated about minimal idioms, not maximal coverage.
- Adding support for diagram tools other than Mermaid. That's a v1.0 conversation, not a v0.x patch — open an issue first.

## Filing an issue

If you're reporting a bad output, please include:

- The prompt you used (verbatim).
- The skill version (commit SHA or release tag).
- The Claude Code version, if you know it.
- The Mermaid output the skill produced.
- A one-sentence statement of what's wrong with it.

If you're proposing a procedure change (a step in `SKILL.md`, a rule in the diagnostic checklist), please include:

- The current behavior.
- The proposed behavior.
- A concrete example where the current behavior fails and the proposed behavior succeeds.

## Filing a PR

Before opening a PR for any non-trivial change, please open an issue first to discuss. The skill is small and opinionated; merging in scope creep is harder to undo than to prevent.

For PRs:

- Keep changes to one of: `SKILL.md`, `references/`, `examples/`, `evals/`. Touch the README only when the change actually affects user-facing documentation.
- Update `CHANGELOG.md`.
- If you change the skill's triggering description, note it explicitly — that's a behavior change for every user.

## Maintenance posture

Honest version: this is a personal project that I use and intend to keep updated, but it's not under any kind of SLA. Issues will be looked at; not all will be fixed. PRs will be reviewed when I can. If you depend on this for something important, pin to a release tag and read the changelog before bumping.
