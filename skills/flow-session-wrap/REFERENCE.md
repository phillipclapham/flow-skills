# flow-session-wrap — Reference

Deep-dive content loaded when SKILL.md instructions need the mechanics
fleshed out. Generalized from FLOW methodology's `WRAP_PROTOCOL.md`
(`github.com/phillipclapham`) for portable, cross-platform use.

---

## FlowScript markers — complete spec with examples

### State markers (active threads)

#### `?` — Question needing decision

```
? Redis vs Postgres for sessions
? Should we launch publicly or keep private
```

#### `thought:` — Insight worth preserving

```
thought: Relations force explicit relationship definition
thought: Hybrid approach emerged naturally through use
```

Confidence prefix optional: `* thought:` (high confidence, validated)
or `~ thought:` (uncertain, exploratory).

#### `✓` — Completed / done

```
✓ Auth system implementation
✓ Documentation updated
```

#### `[blocked(reason, since)]` — Waiting on dependency

**Required fields** — forces explicit tracking:
- `reason`: what blocks progress
- `since`: when the block began (date)

```
[blocked(reason: "waiting on API keys", since: "Jan 10")] Deploy to staging
[blocked(reason: "needs design review", since: "Jan 8")] Feature implementation
```

#### `[parking(why, until)]` — Not ready to process yet

**Recommended fields** (warning if missing, not error):
- `why`: reason for parking
- `until`: when to revisit

```
[parking(why: "not needed until v2", until: "after MVP")] Browser extension
[parking(why: "requires research", until: "Phase 1 done")] Advanced feature
```

#### `[decided(rationale, on)]` — Commitment made

**Required fields** — documents reasoning:
- `rationale`: why this decision
- `on`: when decided (date)

```
[decided(rationale: "user feedback validates need", on: "Jan 10")] Ship minimal version
[decided(rationale: "simplicity wins", on: "Jan 8")] Use Postgres over Redis
```

#### `[exploring]` — Investigating, not committed

```
[exploring] New architecture approach
[exploring(hypothesis: "might improve performance")] Caching layer
```

### Relationship markers (narrative)

#### `->` — Leads to / causes / results in

```
auth bug -> login failures
complexity -> maintenance burden
```

#### `<-` — Derives from / caused by

```
login failures <- auth bug
decision <- user feedback
```

#### `<->` — Bidirectional / mutual influence

```
team size <-> project scope
performance <-> memory usage
```

#### `><[axis]` — Tension / tradeoff

**Axis label REQUIRED** — forces precision on what's being traded:

```
speed ><[velocity vs maintainability] code quality
features ><[stability vs functionality] stability
cost ><[performance vs budget] performance
```

You cannot write bare `><` — the axis label is the discipline.

### Modifiers (prefixes)

| Prefix | Meaning | Use |
|---|---|---|
| `!` | Urgent | `! ? Launch timing — need decision today` |
| `*` | High confidence / proven | `* thought: Evidence validates this works` |
| `~` | Low confidence / uncertain | `~ thought: Not sure but maybe relevant` |
| `++` | Strong positive / emphatic | `++ Love this direction` |

---

## Continuity file structure

The continuity file is a single markdown document with these sections,
in this order:

1. **Partnership** — behavioral instructions for the agent (load-bearing,
   compression-resistant; see "Protected zones" below).
2. **State** — current focus, critical path, parallel threads. REPLACED
   each session.
3. **Action Items** — task surface with lifecycle markers.
4. **Top of Mind** — cognitive salience layer. What should be in
   awareness at next session start.
5. **Recent Context** — compressed narrative of recent work as prose.
   REWRITTEN each session, not appended.
6. **Developing Knowledge** — 1x/2x patterns being watched. 7-day
   half-life; archive if stale and unresolved.
7. **Proven Knowledge** — patterns graduated at 3x. FlowScript-compressed.
   Near-permanent until contradicted by new evidence (then demoted).
8. **Foundation** — near-permanent truths (rarely changes; partnership
   identity, mission, core principles).
9. **Cross-Domain Discoveries** — connections that span multiple
   knowledge domains.

Section operations during wrap (full detail in SKILL.md Step 4):

| Section | Operation |
|---|---|
| Partnership | Touch rarely; behavioral instructions only |
| State | REPLACE entirely |
| Action Items | Update lifecycle markers |
| Top of Mind | Update salience |
| Recent Context | REWRITE narrative |
| Developing Knowledge | Add 1x / increment 2x / graduate 3x → Proven |
| Proven Knowledge | Rarely touch (only graduations from Developing) |
| Foundation | Almost never |

---

## Pattern graduation — full grounding rules

**No pattern graduates without episodic evidence.** This is the immune
system that prevents semantic inbreeding (graduating patterns based on
conversation impression rather than recorded fact).

### Independence rules

- **1x → 2x:** ≥2 independent episodes (different sessions OR different
  agents/sources). Two episodes from the same session restating the same
  observation = one observation, not two.
- **2x → 3x:** ≥3 independent episodes from ≥2 different sources
  (sessions or agents). Cross-source evidence is strongest signal.

### What counts as evidence

- **Episodes from different agents confirming the same pattern** =
  strong (cross-agent validation).
- **Episodes from different sessions confirming the same pattern** =
  good (temporal independence).
- **Episodes from the same session restating the same observation** =
  NOT independent (one observation, not two).

### Grounding annotation format

When promoting a pattern, append inline:

```
!! pattern_name: description ... | 2x (CATEGORY — Apr 10) [evidence: ep-id-1, ep-id-2]
```

The `[evidence: ...]` annotation is load-bearing. Without it, future
compression cannot verify the graduation. A pre-commit hook (or
equivalent quality gate) can mechanically enforce: any 2x/3x marker
without `[evidence: ...]` annotation rejects the commit.

### Quality gate for graduation (all three must pass)

Frequency (3x) is necessary but not sufficient. Three additional gates:

1. **Meta-pattern or surface fact?** Does this change how the agent
   thinks, or is it technical trivia hit 3x? Extract the WHY underneath,
   not the WHAT. Multiple surface facts → single meta-pattern.
2. **Re-discoverable?** Would a 30-second search find this? If yes →
   don't graduate. (CLI flags, framework APIs, platform behaviors.)
3. **Scope-appropriate?** Global wisdom or project-specific? If scoped
   → project file at most, not global continuity.

Compression = abstraction. Graduate the principle, not the observations.

---

## Active demotion — the asymmetric correction mechanism

Most memory architectures only ADD. The immune system DEMOTES.

### Demotion triggers

- A previously-graduated pattern is contradicted by new independent
  evidence
- A pattern's evidence chain doesn't survive re-examination (e.g., the
  cited episodes turn out to be same-session restatement, not independent)
- A pattern was graduated under a stronger claim than the evidence
  actually supports (over-claim)

### Demotion procedure

1. Re-examine the pattern's evidence chain in the episodic store
2. If contradicting evidence is independent AND the prior chain doesn't
   survive: demote to Developing with an annotation explaining why
3. Update any downstream references that depended on the demoted pattern
4. Commit the demotion as a deliberate wrap output — a shrinking
   continuity at a wrap boundary is the system working, not failing

### Why this matters

Without active demotion, bad patterns accumulate forever. With it, the
memory shrinks when shrinking is warranted. The 569-char shrink in a
production wrap is a stronger signal of architectural health than a
569-char expansion — it means the immune system fired and corrected.

---

## Compression guardrails (research-validated)

### Known failure modes

1. **Recency bias.** Recent material feels more important because it's
   vivid. RESIST. A breakthrough from 3 weeks ago matters as much as
   today's work. Proportional weight across timeframes.
2. **Over-compression.** Stripping until meaning is lost. Compress for
   density, not for size targets. Loss is failure.
3. **Performance compression.** Restating the same content in fewer
   words to "look" compressed without removing anything. Same surface
   area with different words is not compression. Real compression:
   pattern graduation + temporal cleanup + format density.

### Safe reduction range: 28-43%

Beyond ~43% reduction in a single pass risks functional loss. One
empirical study found v2.0 of a continuity file at ~70% reduction
caused catastrophic activation failure — the agent could no longer
behave per its Partnership instructions because the causal chains
underneath them got stripped.

The behavioral instructions to the agent (Partnership section causal
chains) are LOAD-BEARING. Removing the WHY behind a behavioral
instruction can cause ~30% persona drift (Li et al. 2024, *Persona
Vectors in Large Language Models*).

### Protected zones — never compress

- **Behavioral instructions to the agent** — causal chains in
  Partnership/Identity sections. Example: `MCAS → ENABLES != PROTECTS
  → handle_cognitive_load`. Removing the `MCAS →` causal antecedent
  collapses the partnership pattern even if `ENABLES != PROTECTS`
  remains.
- **Active state needed for current work** — focus, critical path,
  in-flight decisions.
- **Evidence annotations on graduated patterns** — `[evidence: ep-id]`
  is the audit trail. Stripping it breaks the immune system's
  verification path.

### Compression order (when over budget)

1. **Temporal cleanup first** — archive stale Developing >7d. Lowest-
   value content compresses first; usually clears 5-15% headroom.
2. **Format improvements** — denser FlowScript notation. Use markers
   instead of prose where markers carry the meaning. Compact relationship
   chains (`A -> B -> C`).
3. **Content reduction — LAST RESORT.** Never from activation/
   behavioral instruction zones.

---

## File size budgets (recommendations)

These are observed-stable budgets from production use. Calibrate to
your own context window and pattern density.

| Constraint | Target | Hard limit | Rationale |
|---|---|---|---|
| State section | ~3,000 chars | 4,000 chars | State is REPLACED each session. Growth = previous content didn't compress into Recent Context. |
| Developing block | ≤12 lines | ≤15 lines | If a session legitimately needs more, split into sub-blocks or graduate patterns out. |
| Total file | ~117,000 chars | ~140,000 chars | Drift ceiling. Above this, the cognitive prosthetic becomes a cognitive burden. |
| Soft token target | ~12k tokens | ~15k tokens | At 200k context, this is ~6-7% baseline; leaves room for working context. |

Pre-commit hook (or equivalent) enforces these budgets mechanically.
Bypassing via `--no-verify` (or equivalent) recreates the compression-
becomes-performance drift the hooks exist to prevent.

When to update the budgets: at deliberate partnership-shape changes
(major architectural decisions, methodology revisions), not as
accommodation for drift. Commit message must explain why the budget
moved.

---

## Pre-commit enforcement patterns

The wrap protocol is most reliable when structurally enforced rather
than discipline-enforced. Recommended hook pattern:

### Hook: validate continuity size

```bash
#!/usr/bin/env bash
# .git/hooks/pre-commit (or husky / pre-commit framework equivalent)

if git diff --cached --name-only | grep -q "continuity.md"; then
    python3 /path/to/validate_continuity_size.py
    if [ $? -ne 0 ]; then
        echo "Continuity size validation failed."
        exit 1
    fi
fi
```

The script checks all three budgets (State chars, Developing block
lines, total file chars) and emits actionable error messages pointing
to remediation (temporal cleanup, format improvement, or content
reduction last-resort).

### Hook: validate graduation evidence

```bash
#!/usr/bin/env bash
# Catches 2x/3x markers without [evidence: ...] annotations

if git diff --cached --name-only | grep -q "continuity.md"; then
    python3 /path/to/validate_continuity_evidence.py
    if [ $? -ne 0 ]; then
        echo "Ungrounded 2x/3x patterns detected. Add evidence annotation or demote."
        exit 1
    fi
fi
```

The principle: **structural invariants beat discipline-based
verification.** Discipline drifts under load; structural enforcement
catches drift at the commit boundary.

---

## Bilateral cross-substrate integration (multi-agent setups)

If running multi-agent with agents that need to share patterns across
substrates without contaminating each other's memory, use a **bilateral
blackboard** pattern:

### Architecture

- Each agent maintains its own continuity file (per-agent isolation,
  non-negotiable)
- A shared "blackboard" surface (database table, JSON file, etc.)
  receives cross-agent observations with structured metadata: source
  agent, target agent or broadcast, type, evidence
- Each agent reads the blackboard at wrap time and decides for itself
  whether to integrate the entry
- Own-side synthesis: agent B's integration of agent A's blackboard
  entry happens at B's wrap, in B's own framing — A does not write
  directly to B's continuity

### Wrap integration step (insert between Step 2 and Step 3 in SKILL.md)

For each unreviewed blackboard entry:

- **Fold as new 1x:** add to Developing Knowledge with source
  annotation `[via blackboard, <agent>-<id>, <date>]`
- **Fold as evidence for existing pattern:** append `[evidence:
  blackboard-<agent>-<id>]` to existing pattern's evidence annotation;
  increment marker if grounding supports graduation
- **Skip:** if duplicative or already folded
- **Defer:** if entry needs more development; leave in queue with
  deferred annotation

Track a review cursor (timestamp marker) at the top of the queue file
so multiple wraps in one day don't re-fold same entries.

### Why this is the right boundary

Direct cross-agent continuity writes break per-agent isolation and
contaminate per-agent memory with other agents' framing. The
blackboard is the architecturally correct boundary: agents coordinate
without compressing into shared global state.

---

## Philosophical foundation — Complementary Learning Systems

The wrap protocol operationalizes the Complementary Learning Systems
(CLS) model from cognitive science (McClelland, McNaughton, O'Reilly
1995; extensive subsequent literature).

### The model in brief

Biological memory has two complementary systems:

- **Hippocampus** — fast learning, episodic, high-resolution event
  storage, capacity-limited
- **Neocortex** — slow learning, semantic, compressed schemas,
  near-unlimited capacity

Consolidation happens during sleep (and at lower bandwidth during
wakeful rest): hippocampal episodes get replayed and the patterns get
integrated into neocortical schemas. The hippocampus is where
experience first lands; the neocortex is where understanding
accumulates.

### How the wrap protocol maps

- **Episodic store** = hippocampus. Fast write, high-resolution, every
  event captured. Cheap to add to.
- **Continuity file** = neocortex. Slow write (only at wrap boundaries),
  compressed, schematic. Patterns integrate into structured knowledge.
- **Wrap procedure** = consolidation. Move evidence from episodic
  (Layer 1) to continuity (Layer 2) via pattern extraction + graduation
  + integration.
- **Immune system + graduation gates** = the quality control that
  biological memory does implicitly (only well-supported patterns
  consolidate). Made explicit here because LLM memory architectures
  don't have this built in — has to be engineered.

### Why this matters for memory architecture

Most LLM memory systems are hippocampus-only (flat retrieval, RAG
over an event log) OR neocortex-only (continuity file that gets
appended forever without consolidation). Both produce known failure
modes:

- **Hippocampus-only** → retrieval finds episodes but no abstraction
  accumulates. Same problems get re-solved.
- **Neocortex-only** → schemas accumulate without grounding. Patterns
  emerge from impression rather than fact. Semantic inbreeding.

The two-layer architecture with explicit consolidation is what
produces compression-with-intelligence rather than either failure
mode.

---

## Anti-patterns checklist (run before declaring wrap complete)

Before commit, verify:

- [ ] Step 1 (Episodic extraction) actually happened — there are
      episodes from this session in the store
- [ ] Every 2x/3x graduation has `[evidence: ...]` annotation pointing
      to real episode IDs
- [ ] No pattern was graduated on same-session restatement
- [ ] State and Top of Mind were REPLACED, not appended
- [ ] Recent Context was REWRITTEN, not appended
- [ ] Temporal cleanup ran (stale Developing >7d → archive)
- [ ] If any pattern got demoted, the demotion is annotated with reason
- [ ] If continuity size is over budget, compression order was
      followed (temporal cleanup → format → content-reduction
      last-resort)
- [ ] No behavioral instruction causal chains were compressed
- [ ] Commit message describes what changed (key graduations, key
      demotions, key state changes)

If any of these are uncertain, re-run the relevant step. A wrap that
declares done while skipping these is wrap-shaped output without
wrap meaning.
