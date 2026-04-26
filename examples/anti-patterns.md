# Anti-patterns — what this skill exists to prevent

A catalog of failure modes commonly seen in AI-generated system diagrams, with concrete descriptions of how each one manifests and why prompting harder usually doesn't fix it. The skill's procedure is designed to prevent each one structurally.

## The noun inventory

The most common failure. Every named component from the user's prompt or codebase becomes a box, with equal visual weight. A diagram of a 14-service system gets 14 boxes, all the same size, all in roughly the same layer of the layout.

**How it manifests:**
- Diagrams with 12+ nodes, all roughly equal weight
- Components from the source material appear as boxes whether or not they're on the trace being illustrated
- The reader cannot tell which components are core to the flow and which are auxiliary

**Why it happens:** the model defaults to drawing the *inventory* of a system because that's what the prompt enumerates. Without explicit guidance to pick a trace, completeness wins over clarity.

**The fix (step 2 of the procedure):** name a concrete trace before drawing. Components not on the trace either get pushed to a sibling diagram or omitted entirely.

## The layer cake

Three or more horizontal bands stacked vertically, with arrows mostly going downward. The bands typically follow architectural-abstraction levels ("frontend / app / data") or organizational layers ("user-facing / platform / infrastructure").

**How it manifests:**
- Tool adapters end up "below" the orchestrator that calls them, even though the call relationship is sideways
- Audit logs end up at the bottom even though they're written to from everywhere
- Cross-cutting concerns get a footer band ("isolation: KMS, PII middleware, audit chain")

**Why it happens:** abstraction layers are the easiest mental model to translate to a diagram, but they conflate "what calls what" with "what's at what level of abstraction." A function that's called from inside a high-level component isn't *below* that component — it's *inside* it.

**The fix:** use spatial position to encode call order or trust boundary, not abstraction level. Subgraphs group by semantic axis (step 2). Tool adapters drawn adjacent to the components that call them, not below.

## Decorative color

Boxes are colored, but the colors don't map to a single semantic axis. Two boxes share a color because they happen to be in the same row, not because they belong to the same category.

**How it manifests:**
- Adjacent boxes share fills with no consistent reason
- The same component category appears in different colors across the diagram
- A reader cannot articulate what the colors mean

**Why it happens:** color is treated as visual variety rather than encoding. The model picks colors to make the diagram look interesting rather than to communicate.

**The fix:** declare a single semantic axis (trust boundary, control vs data plane, sync vs async, internal vs external). Apply colors by category along that axis. If you can't articulate the axis in one sentence, go monochrome.

## Cross-cutting concerns drawn as peer boxes

Authentication, PII redaction, rate limiting, audit logging, and observability instrumentation are drawn as services in the same layer as the components they intercept.

**How it manifests:**
- An "Auth" box with arrows to and from every other component, creating a star pattern that dominates the layout
- A "PII redaction" box that the orchestrator appears to *call* rather than *pass through*
- An "audit log" written to from four separate components, drawn as four separate arrows to four positions on the canvas
- A "metrics" service that creates false call relationships ("does the orchestrator depend on metrics for execution?")

**Why it happens:** the model treats every named system as a participant. Cross-cutting concerns *intercept* a flow rather than *participate* in it, but that distinction isn't in the prompt.

**The fix:** redraw cross-cutting concerns as labels on the edges they intercept (PII redaction on the request edge), as gates mid-edge, or as a single sink node with multiple dotted incoming arrows (one audit log, four arrows in).

## Architectural choices buried in subtitles

Real design decisions — "cost-aware tier selection," "rules-then-LLM verification," "lazy skill loading" — appear as parenthetical text inside boxes, in 10pt font, where they're easily missed.

**How it manifests:**
- A "Verifier (rules-first, LLM fallback)" box that hides the most interesting architectural choice
- "LLM Router (cost tiers)" with no visual indication of how the routing works
- "Skill Registry (lazy load)" with no indication of when the lazy load happens

**Why it happens:** the model treats subtitles as documentation, but architectural decisions deserve to be visible structurally — drawn as branches, decision diamonds, or split nodes.

**The fix:** if a design choice matters, surface it visually. A verifier with two paths becomes a decision diamond with two outgoing arrows labeled with the conditions. A cost-aware router becomes a fan-out to "fast / cheap" and "slow / accurate." See `references/mermaid-patterns.md` for specific patterns.

## Missing return arrow

A request goes in, no response comes out. The user is shown initiating something but never receiving anything back.

**How it manifests:**
- An entry-point user box with arrows going out, no arrows coming back
- A trace that ends at a "response" label with no edge attached
- The audit log appears to be the final destination of the request

**Why it happens:** the model draws the forward path of the trace and forgets that user-facing systems need to close the loop.

**The fix:** every diagram with a user entry point needs a labeled return edge to that user. Even a one-word "response" arrow is critical.

## Multiple cadences in one frame

The diagram shows the synchronous request flow, the async batch retraining, the failure recovery loop, and the observability pipeline all at once. They run on different time scales (milliseconds, minutes, days) and they confuse each other visually.

**How it manifests:**
- A retraining loop drawn alongside a per-request flow with no visual distinction
- Failure paths drawn as peer flows to the happy path
- A diagram that the reader cannot answer "is this a per-request flow or a per-day flow" about

**Why it happens:** the model tries to be complete. The user mentioned all of these things, so all of them go on the canvas.

**The fix:** pick one cadence per diagram. Mention the others as candidates for sibling diagrams. The skill's procedure (step 1) names this choice explicitly.

## Edge spaghetti as a structural symptom

Multiple arrows cross in the same region, often where the diagram tries to do too much in too little space.

**How it manifests:**
- Arrows crossing through unrelated boxes
- Floating labels with no clear edge attachment
- Routing that goes around the long way for no apparent reason

**Why it happens:** edge spaghetti is usually a *symptom* of one of the failure modes above (noun inventory, layer cake, multiple cadences), not a cause. The model has too much content and too little space, and the layout engine can only do so much.

**The fix:** address the structural failure mode first. Edge crossings drop nonlinearly when you remove the components that shouldn't be there. After the structural fix, switch to elk for the routing pass — but elk can't fix a diagram that's structurally trying to show too much.

## Why prompting harder rarely works

A common pattern: the user iterates the prompt, adding instructions like "label every edge," "use color to encode trust boundaries," "show the response path." Each iteration adds *semantic content* without giving the model better tools for *layout*.

The result is often a diagram that is more complete, more correct, and *less* readable than the previous version, because added arrows compound the routing problem.

The structural fix is to:
1. Pick the right tool (one with a real layout engine, like Mermaid + elk or Graphviz).
2. Plan the trace in plain text *before* writing diagram source, so structural decisions are made when they're cheap to change.
3. Use the diagnostic checklist (`references/diagnostic-checklist.md`) on existing diagrams to name the failure mode before iterating.

This skill encodes that procedure so users don't have to reinvent it.
