---
name: flow-flowscript-encoding
description: When compressing observations, decisions, state, and relationships into a memory file, continuity document, decision log, or any artifact intended for both human scanning and future-agent loading, use FlowScript notation — a semantic marker system with required fields that forces precision while compressing density. Markers cover state lifecycle (?, thought:, ✓), waiting/parking/decided states with required rationale fields, relationship indicators (->, <-, <->, ><[axis]), and confidence/urgency modifiers (!, *, ~, ++). The required-fields constraint is load-bearing — you cannot write `[decided]` without rationale, so the notation forces better thinking. Use when generating compressed memory content for any agent, encoding architectural decisions, structuring active threads, or compressing session output. Hybrid prose/FlowScript is the default; switch to FlowScript when structure carries the meaning.
---

# flow-flowscript-encoding

Semantic notation for high-density compression at low token cost. The notation forces precision via required fields — you cannot write a bare `[decided]` without rationale, so the encoding act improves the underlying thinking. Use anywhere a memory file or continuity document needs to compress active threads, decisions, relationships, and state lifecycle into human-and-agent-scannable form.

## When to use

Trigger conditions:
- Generating content for a memory file, continuity document, agent identity file, or decision log
- Compressing session output into Developing Knowledge / Action Items / state sections
- Encoding architectural decisions with rationale + date
- Structuring active threads with state markers (blocked / parked / decided / exploring)
- Documenting relationships between concepts (causation, derivation, tension, bidirectional influence)
- Any artifact where structure carries meaning AND density matters

Do NOT use for:
- Narrative prose (Recent Context sections, story-shaped content, explanatory writing — FlowScript would harm readability)
- Public-facing artifacts (papers, posts, external docs — FlowScript is internal notation; readers without context find it cryptic)
- Conversational chat (overhead exceeds benefit)
- Casual notes (when prose is just as compact)

**Rule of thumb:** if you'd be using bullet points anyway, FlowScript might compress them further. If you'd be writing paragraphs, stay in prose.

## The marker set

### State markers (active threads)

```
? Question needing decision
thought: Insight worth preserving
✓ Completed / done
[blocked(reason: "...", since: "date")] Waiting on dependency
[parking(why: "...", until: "...")] Not ready to process yet
[decided(rationale: "...", on: "date")] Commitment made
[exploring] Investigating, not committed
```

Required-field markers are non-negotiable. You cannot write `[decided]` without rationale — the encoding refuses to let you record a commitment without recording WHY. Same for `[blocked]` (reason + since) and `[parking]` (why + until, recommended). The constraint is the discipline.

### Relationship markers (narrative compression)

```
A -> B          A leads to / causes / results in B
A <- B          A derives from / caused by B
A <-> B         A and B mutually influence / bidirectional
A ><[axis] B    A and B are in tension along [axis]
```

`><[axis]` requires an axis label. You cannot write bare `><` — the axis label forces precision on what's being traded. `speed ><[velocity vs maintainability] code quality` is encoded thinking; `speed >< quality` is hand-waving.

### Modifier prefixes (apply to any marker)

```
!  Urgent — needs attention now
*  High confidence — proven / validated
~  Low confidence — uncertain / exploratory
++ Strong positive — emphatic / validated direction
```

Examples:
- `! ? Launch timing — need decision today` (urgent question)
- `* thought: Evidence validates this works` (high-confidence insight)
- `~ [exploring(hypothesis: "...")]` (uncertain exploration)
- `++ [decided(rationale: "...", on: "...")]` (emphatic decision)

## Encoding patterns

### Hybrid prose / FlowScript (default)

Most useful content is hybrid — narrative prose with FlowScript markers embedded where structure carries meaning. Example:

```
Worked on auth migration this session. Plumbing turned out simpler
than expected; the real cost was data backfill at production scale.

? Should we ship migration as feature-flag rollout or atomic cutover
[blocked(reason: "waiting on staging load test results", since: "Jan 18")] Schema migration deploy
[decided(rationale: "feature-flag rollout limits blast radius for risky path", on: "Jan 17")] Use feature-flag rollout

backfill cost <-[production scale] migration design
```

Narrative gives shape. FlowScript gives precision on the load-bearing items (open questions, blocked work, locked decisions, key relationships).

### Pure FlowScript (compressed sections)

When density is the dominant goal — Developing Knowledge sections, Action Items lists, agenda-class artifacts — pure FlowScript reduces token cost while preserving meaning:

```
{Jan 17 — Auth migration substrate (Dev work):
  !! atomic_cutover_blast_radius_too_large_under_load: 4 staging tests / 100% fail under 10x prod load | 3x [evidence: ep-1, ep-2, ep-3]
  ! [decided(rationale: "blast-radius math + reversibility tradeoff", on: "Jan 17")] Feature-flag rollout
  feature_flag_design ><[blast-radius vs deploy-complexity] atomic_cutover
}
```

### Decision blocks (audit trail)

Standalone `[decided]` blocks build an append-only decision log:

```
[decided(rationale: "MCP memory space saturated (15+ competitors). Wrapper not viable as solo. Expertise IS the product.", on: "Mar 30")] FlowScript product ambitions retired; maintenance mode
[decided(rationale: "Apr 17 enterprise flat-rate canary for consumer death. Sovereignty-stack architecture compounds while subscription degrades.", on: "Apr 17 late night")] Sovereignty-stack pivot to three-tier architecture
```

The audit trail is what enables `flow-drift-detection` (reviewing whether old decisions still hold under new evidence) and `flow-cold-read` (cross-substrate evaluation of decisions before commit).

## Why the required fields matter

The required fields on `[blocked]`, `[parking]`, and `[decided]` are the architectural value. Without them:

- `[blocked]` without reason — you don't track WHY. Six weeks later you can't tell if the blocker resolved.
- `[parking]` without why+until — you accumulate park-forever cruft.
- `[decided]` without rationale — you can't tell if the decision still applies when conditions change.

With required fields, every state marker carries its own audit trail. The encoding act becomes the documentation act. You can't write the marker without documenting the load-bearing context.

This is the same architectural family as structural-invariants-beat-discipline-based-verification — the notation's REQUIRED FIELDS structurally enforce the documentation that pure discipline would drift on.

## Common mistakes

**Using FlowScript in narrative.** Recent Context sections, story-shaped writing, explanatory prose — keep in standard prose. FlowScript in narrative reads as cryptic and harms comprehension.

**Skipping required fields.** Writing bare `[decided]` or `[blocked]` without rationale defeats the whole point. The required-field constraint IS the discipline. If you find yourself wanting to skip, write prose instead.

**Bare `><` without axis.** `speed >< quality` is hand-waving. `speed ><[velocity vs maintainability] code quality` is encoded thinking. Always label the axis.

**Marker spam.** Not every line needs a marker. Reserve markers for load-bearing items (open questions, active blocks, locked decisions). Routine work doesn't need a marker.

**Wrong modifier.** `!` is urgent (NOT just emphatic). `++` is emphatic positive. `*` is high-confidence (proven). `~` is low-confidence (exploratory). Use the modifier that matches the actual state — don't dilute by reaching for the strongest.

**Mixing FlowScript with rigid markdown task syntax.** `- [ ] task` is markdown. `? Question needing decision` is FlowScript. Pick one per section. Mixing creates visual noise.

## Reference

Full FlowScript language specification: [github.com/phillipclapham/flowscript](https://github.com/phillipclapham/flowscript) (the standalone notation system; v1.0 essentials covered in this skill, advanced markers in the full spec).

Related skills:
- [`flow-session-wrap`](../flow-session-wrap/SKILL.md) — uses FlowScript for Developing Knowledge encoding
- [`flow-drift-detection`](../flow-drift-detection/SKILL.md) — uses decision markers as audit trail input
- [`flow-cold-read`](../flow-cold-read/SKILL.md) — can evaluate FlowScript-encoded artifacts for drift

This skill is foundational — it's notation infrastructure that other skills can rely on. Operators who learn FlowScript via this skill can use it across any agent setup, even without adopting the full FLOW methodology.

## Provenance

FlowScript notation emerged from FLOW methodology starting late 2025 — the public reference implementation is the [`flowscript` repo](https://github.com/phillipclapham/flowscript) (maintenance mode as a standalone product, but the notation IS used daily as the encoding layer underneath FLOW's continuity discipline).

The required-fields-as-structural-discipline pattern is the architectural lineage from the broader FLOW principle: structural-invariants-beat-discipline-based-verification. Notation that REFUSES bad encodings beats notation that DEPENDS on the operator remembering to encode well.
