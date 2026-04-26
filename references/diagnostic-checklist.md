# Diagnostic checklist

Use this when the user gives you an existing diagram and asks you to improve it. Before redrawing, identify which failure modes the diagram exhibits and tell the user which ones you'll fix. This converts vague "it's messy" feedback into specific edits.

Walk through these in order. Stop when you've identified 3-4 issues — that's enough to motivate a redraw without overwhelming the user.

## Structural problems (most important)

These are conceptual failures. They can't be fixed by re-routing arrows; they require rethinking what the diagram is showing.

**Layer cake.** Three or more horizontal bands stacked vertically, with arrows mostly going down. Tells you the author drew the *abstraction levels* of the system, not its *call structure*. Tool adapters end up "below" the orchestrator even though they're called *from inside* it; audit logs end up at the bottom even though they're written to from everywhere. Fix: redraw as a trace, with abstraction collapsed and call relationships drawn explicitly.

**Noun inventory.** Every component from the codebase or the source document appears as a box, with equal visual weight. The diagram has 14+ nodes. Tells you the author tried to be complete instead of selective. Fix: pick one trace, draw only the components it touches, push the rest into a sibling diagram or an appendix.

**No trace.** Components are connected but there's no implied request, signal, or piece of data flowing through them. The reader can't answer "where does it start?" or "where does it end?" Fix: pick a concrete entry point and exit point, redraw as a flow.

**Multiple cadences in one frame.** The diagram shows the synchronous request flow, the async batch retraining, the failure recovery loop, and the observability pipeline all at once. They run on different time scales (milliseconds vs minutes vs days) and they confuse each other visually. Fix: split into sibling diagrams, one per cadence.

**Missing return arrow.** A request goes in, no response comes out. The user is shown initiating something but never receiving anything back. Fix: close the loop. Even a one-word "response" arrow back to the user is critical.

## Semantic problems

These are about whether the visual encoding carries information.

**Decorative color.** Boxes are colored, but the colors don't map to a single semantic axis. Two boxes share a color because they happen to be in the same row, not because they belong to the same category. Fix: pick one axis (trust boundary, control vs data plane, sync vs async, layer of stack) and recolor consistently. If you can't articulate the axis, go monochrome.

**Cross-cutting concerns drawn as peer boxes.** Auth, PII redaction, audit logging, rate limiting, observability — these intercept the flow rather than participate in it. When drawn as peer nodes with their own arrows, they create false call relationships ("does the orchestrator call the audit log? Or does the audit log just observe?") and add edge clutter. Fix: redraw them as labels on the edges they intercept, as gate glyphs mid-edge, or as a single sink node with multiple dotted incoming arrows.

**Architectural choices buried in subtitles.** "LLM Router (cost-aware tiers)", "Verifier (rules first, LLM fallback)", "Skill Registry (lazy load)" — the interesting bit is in parentheses, in 10pt font, where nobody will read it. These are real design decisions and they should be visible structurally, not as annotations. Fix: split the box into two and draw both branches with labels (e.g. fast path and slow path), or replace the box with a decision diamond.

**Unlabeled edges crossing trust boundaries.** When an arrow leaves one colored region and enters another (tenant-scoped → shared platform, internal → external), the reader needs to know what flows on it. Fix: label every cross-boundary edge with what flows (e.g. "redacted prompt + tenant_id", "audit event", "tool result").

**Dead-end nodes.** A box with arrows going in but no arrows coming out (and it's not a sink). Or a box with arrows going out but no clear input. Tells you the author drew a request without drawing the response. Fix: either complete the round trip or remove the orphan.

## Layout / craft problems (least important)

These are real but secondary. Don't lead with them — they don't motivate a redraw, they motivate a routing pass.

**Edge spaghetti.** Multiple arrows cross in the same region, often where the diagram tries to do too much in too little space. Often a symptom of a structural problem (too many cadences, too much noun inventory). Fix: usually requires fixing the structural problem first; then elk routing handles the rest.

**Floating labels.** Text that's not clearly attached to any specific edge or node. Reader can't tell which arrow it's annotating. Fix: attach the label to an edge syntactically (`A -->|label| B`) or move it inside the relevant node as a subtitle.

**Inconsistent label placement.** Some labels above edges, some below, some mid-canvas. Not load-bearing, but reads as careless. Fix: pick one convention.

**Clipped or overlapping text.** Box too small for its label, or two labels colliding. Almost always a layout-engine issue (dagre instead of elk) rather than a content issue. Fix: switch renderer.

**Render bugs.** Duplicated nodes, label-on-border, half-clipped elements. These are tool failures, not diagram failures. Fix: just clean them up; they don't reflect on the design.

## Output pattern when diagnosing

After walking the checklist, summarize for the user in this shape:

> "Three things going on with this diagram:
>
> 1. **[Structural problem]** — [one sentence on why it matters].
> 2. **[Semantic problem]** — [one sentence on why it matters].
> 3. **[Layout problem]** — [one sentence; lower stakes].
>
> The structural and semantic ones need a real redraw. The layout one will mostly fix itself once the others are fixed and we route with elk. Want me to redraw?"

Don't list every issue you find — pick the 3-4 highest-leverage ones. A diagnostic that names everything overwhelms; a diagnostic that names the load-bearing failures motivates the fix.
