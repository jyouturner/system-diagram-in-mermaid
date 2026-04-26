# system_diagram_in_mermaid

An [Agent Skill](https://docs.claude.com/en/docs/claude-code/skills) for drawing system architecture diagrams that are actually legible — using Mermaid as the rendering target and elk as the layout engine.

## What this fixes

The default failure mode for AI-generated system diagrams is the *noun inventory*: every component as a box, every relationship as an arrow, color used decoratively, layers stacked vertically as if hierarchy were the same as call order. The result reads like an org chart of the codebase, not like an explanation of what the system does.

This skill replaces "draw boxes for every component you see" with a five-step procedure:

1. **Pick the diagram's job** — trace, topology, failure path, or cross-cadence loop. Don't mix them.
2. **Plan the trace in plain text first** — concrete entry point, ordered path, semantic axis, out-of-scope fence.
3. **Map to Mermaid primitives** — components on the path become nodes; semantic groups become subgraphs; cross-cutting concerns become edge labels, not boxes.
4. **Render with elk, not dagre** — elk minimizes edge crossings and handles subgraphs well.
5. **If iterating on a messy diagram, diagnose first** — name the failure modes before redrawing.

## Install

Drop the skill into your Claude Code skills directory:

```bash
git clone https://github.com/jyouturner/system-diagram-in-mermaid.git ~/.claude/skills/system_diagram_in_mermaid
```

Or vendor it into a project:

```bash
git clone https://github.com/jyouturner/system-diagram-in-mermaid.git .claude/skills/system_diagram_in_mermaid
```

Claude Code auto-discovers skills under `~/.claude/skills/` and `<project>/.claude/skills/`. The skill description tells Claude when to use it — you don't need to invoke it explicitly.

## Files

- [`SKILL.md`](./SKILL.md) — the skill definition and procedure.
- [`references/diagnostic-checklist.md`](./references/diagnostic-checklist.md) — failure modes to identify before redrawing an existing diagram.
- [`references/mermaid-patterns.md`](./references/mermaid-patterns.md) — specific Mermaid idioms (subgraphs, classDef, linkStyle, syntax footguns, standalone HTML render template).

## When it triggers

Claude will invoke this skill on requests like:

- "Draw a diagram of [system]"
- "Show me how [X] works" — multi-component systems
- "Architecture of [project]" / "request flow for [feature]"
- "What does [agent / pipeline / service mesh] look like end to end"
- "This diagram is messy, can you redraw it" (iteration mode)

It will *not* trigger for ER diagrams, sequence diagrams, state machines, or pure logic flowcharts — Mermaid has dedicated diagram types for those.

## License

MIT.
