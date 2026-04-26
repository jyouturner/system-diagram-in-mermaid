# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [0.2.0] — 2026-04-26

### Added
- `LICENSE` file (MIT) — was claimed in README but missing from the tree.
- `CONTRIBUTING.md` with guidance on what kinds of issues and PRs are most useful.
- `CHANGELOG.md` (this file).
- `examples/walkthrough.md` — a worked example applying the procedure to a multi-tenant RAG service, including the plan-then-source workflow.
- `examples/anti-patterns.md` — a catalog of failure modes commonly seen in AI-generated system diagrams, with concrete descriptions of how each one manifests.
- `evals/evals.json` — small set of test prompts exercising different diagram archetypes (request trace, batch pipeline, iteration redraw, out-of-scope ERD, ML pipeline).
- README sections: "Before / after," "Try it," "Limitations," "Rendering caveats," "Evaluation."

### Changed
- README restructured to lead with a concrete before/after comparison.
- README is now explicit about scope (C4 container level, request-trace bias) and renderer dependencies (elk vs dagre).
- Repo identifier normalized: `system-diagram-in-mermaid` (hyphens) used consistently in install paths and file references.

### Notes
- No changes to `SKILL.md` or `references/` content. The skill itself is unchanged from v0.1; this release is documentation, examples, and project hygiene.

## [0.1.0] — initial commit

### Added
- `SKILL.md` with the five-step procedure.
- `references/diagnostic-checklist.md` — failure modes for diagnosing existing diagrams.
- `references/mermaid-patterns.md` — Mermaid idioms for system diagrams.
- Initial README with install instructions and trigger examples.
