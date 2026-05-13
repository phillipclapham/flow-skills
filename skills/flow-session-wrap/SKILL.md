---
name: flow-session-wrap
description: At the end of a meaningful work session, compress what happened into a structured continuity update using FLOW methodology — extract episodes (factual record), update state and top-of-mind salience, rewrite recent narrative, evaluate which developing patterns graduate to proven via citation-validated 3x rule, demote patterns that no longer hold under new evidence, archive temporally stale material, and commit. Use when the user says "wrap", "update continuity", "close out the session", or when a meaningful work unit completes (feature shipped, architectural decision made, multi-step research concluded). Produces compression-with-intelligence rather than appending-without-thinking — memory gets smarter (not just longer) as work accumulates.
---

# flow-session-wrap

End-of-session memory consolidation protocol from FLOW methodology. Produces a continuity file that gets SMARTER as work accumulates rather than just longer. The mechanism is **graduation logic + immune system + structurally-enforced compression**. Without these, memory accumulates and process degrades. With them, the system corrects itself and patterns that prove out become reusable primitives.

## When to use

Trigger conditions:
- User says "wrap", "wrap the session", "update continuity", "close out", "end of session"
- A meaningful work unit just completed (feature shipped, decision made, research conclusion reached, multi-step task finished)
- A natural completion boundary AND non-trivial work happened since the last wrap

Do NOT use for:
- Mid-execution checkpoints (use a lighter "save state" pattern — wraps are cognitive events, not save events)
- Trivial sessions (single quick question + answer)
- Sessions where compression material is too thin (graduation criteria won't fire on noise)

## What this skill assumes

This skill assumes the agent has:

1. A **continuity file** (markdown) where compressed memory lives between sessions.
2. An **episodic store** — any append-only event log that supports timestamped typed entries with tags and search. SQLite, JSONL, structured markdown, or equivalent.
3. **Version control** for the continuity file (git or equivalent). Compression is lossy by design; the full transcript lives in commit history.

If any of these are missing, set them up before running the wrap. The protocol assumes their existence.

## The six-step wrap procedure

### Step 1: Episodic extraction

Before compressing anything into continuity, write the session's facts to the episodic store. **Episodes are FACTUAL and SEARCHABLE; continuity patterns are OPINIONATED and COMPRESSED. The episode comes first.**

For each meaningful event in the session, write a typed episode:

- **finding** — a discovery, verified fact, observation about reality
- **decision** — a choice made, with rationale
- **observation** — a pattern noticed (not yet evidence-validated)
- **connection** — a cross-domain link
- **outcome** — what resulted from a decision or experiment
- **tension** — a contradiction or paradox surfaced

Each episode gets: content (what happened), source (session ID or conversation reference), tags (domain keywords), timestamp.

**Target: 3-10 episodes per meaningful session.** Zero episodes means this step got skipped — even "we decided X because Y" is an episode worth writing. Continuity downstream depends on this fact-base.

### Step 2: Parse and identify candidates

Review the session and identify:

- New patterns to add to Developing Knowledge (1x marker)
- Existing 1x patterns that fired again (1x → 2x candidates)
- Existing 2x patterns that fired with independent evidence (2x → 3x graduation candidates)
- Previously-graduated patterns that may no longer hold under new evidence (demotion candidates)
- Stale developing patterns >7 days old (archival candidates)

### Step 3: Validate graduation evidence (the immune system — NON-NEGOTIABLE)

**No pattern graduates without episodic evidence.** This is the immune system that prevents semantic inbreeding — graduating patterns based on conversation impression rather than recorded fact.

For every pattern being promoted:

- **1x → 2x:** at least 2 independent episodes (different sessions OR different sources). Two episodes from the same session restating the same observation = one observation, not two.
- **2x → 3x:** at least 3 independent episodes from at least 2 different sources (sessions or agents). Cross-source evidence is the strongest signal.

Search the episodic store for supporting episodes. **If insufficient:**

- The pattern stays at its current marker — no graduation, no exceptions
- OR write the episode now if Step 1 missed it (then re-check)

When promoting, annotate inline so grounding survives future compression passes:

```
!! pattern_name: description ... | 2x (CATEGORY) [evidence: ep-id-1, ep-id-2]
```

The `[evidence: ...]` annotation is load-bearing. Without it, future compression can't verify the graduation, and the pattern becomes ungrounded under audit.

### Step 4: Update the continuity file

Six section operations, each with its own update semantics:

1. **Current State → REPLACE entirely** with today's focus + critical path. Not append. Replace. State is "what's true now," not a log of past states. If State is growing across wraps, previous session content didn't get compressed into Recent Context — fix there, not by extending State.

2. **Action Items → update lifecycle markers.** Mark completed items done. Update staleness fields (`seen:` / `next:`). Remove items that resolved naturally.

3. **Top of Mind → update cognitive salience.** What should be in awareness at next session start? Add new items, remove items that absorbed into Proven knowledge or completed.

4. **Recent Context → REWRITE as prose narrative.** Compressed prose describing recent work as a single shape, not a transcript. Rewriting (vs appending) auto-compresses by construction — previous narrative gets folded into a momentum-pointer summary if still relevant, dropped if absorbed elsewhere.

5. **Developing Knowledge → add 1x / increment 2x / graduate 3x → Proven.** Add new patterns from this session. Increment patterns that fired again (with evidence annotation per Step 3). Graduate 2x patterns with sufficient evidence to Proven (extract from Developing, FlowScript-compress, place in Proven section).

6. **Temporal cleanup.** Archive Developing patterns >7 days stale. Archive Proven patterns >30 days stale. Git preserves them; archival is for the working file's clarity.

### Step 5: Active demotion (the immune system in motion)

Before committing, ask: **did anything in this session contradict a previously-graduated pattern?**

If yes:

- Re-examine the Proven pattern's evidence chain
- If the contradicting evidence is independent AND the prior evidence chain doesn't survive scrutiny, **demote the pattern back to Developing** with an annotation explaining why
- Update any downstream references that depended on the demoted pattern

This is the asymmetric correction mechanism that flat-retrieval memory architectures structurally lack. Without it, bad patterns accumulate forever. **With it, the continuity file is allowed to shrink — and a shrinking continuity at a wrap boundary is evidence the immune system is working.**

### Step 6: Commit

Commit the continuity file changes to version control. Commit message should reference the session and the key change (e.g., "Session wrap May 13 — graduated `pattern-X` Proven, demoted `pattern-Y` back to Developing"). Push to remote if applicable.

## FlowScript markers (compression vocabulary)

Use these in Developing Knowledge, Action Items, and decision annotations. They make compressed content scannable and force precision on what's being tracked.

| Marker | Meaning | Required fields |
|---|---|---|
| `?` | Question needing decision | — |
| `thought:` | Insight worth preserving | — |
| `✓` | Completed | — |
| `[blocked(reason, since)]` | Waiting on dependency | `reason`, `since` |
| `[parking(why, until)]` | Not ready to process yet | `why`, `until` (recommended) |
| `[decided(rationale, on)]` | Commitment made | `rationale`, `on` |
| `[exploring]` | Investigating, not committed | — |
| `->` | Leads to / causes | — |
| `<-` | Derives from | — |
| `<->` | Bidirectional / mutual | — |
| `><[axis]` | Tension / tradeoff | axis label REQUIRED |

**Modifiers (prefixes):** `!` urgent, `*` high confidence, `~` uncertain, `++` strong positive.

Required-field markers like `[decided(rationale, on)]` force precision — you cannot write a bare `[decided]` and skip the reasoning. The constraint produces better thinking.

Full FlowScript reference: see [REFERENCE.md](./REFERENCE.md) or `github.com/phillipclapham/flowscript`.

## Compression discipline (the non-negotiables)

Compression has known failure modes:

1. **Recency bias.** Recent material feels more important because it's vivid. RESIST. A breakthrough from 3 weeks ago matters as much as today's work.
2. **Over-compression.** Stripping until meaning is lost. Compress for density, not for size targets. Loss is failure.
3. **Performance compression.** Restating the same content in fewer words to "look" compressed without removing anything. The pre-commit hook (or equivalent quality gate) should catch this — if you don't have one, the discipline is on the operator.

**Compression order when over budget:**

1. **Temporal cleanup first** — archive stale Developing >7d. Lowest-value content compresses first.
2. **Format improvements** — denser FlowScript notation, fewer words for same meaning.
3. **Content reduction — LAST RESORT.** Never from activation/behavioral instruction zones.

**Protected zones — never compress:**

- Behavioral instructions to the agent (causal chains in partnership/identity sections)
- Active state needed for current work
- Evidence annotations on graduated patterns (these are the audit trail)

## Common mistakes

**Skipping episodic extraction.** Result: patterns graduate based on conversation impression. Semantic inbreeding. The immune system has no fact-base to verify against. **The episode comes first. Always.**

**Graduating on same-session restatement.** Two mentions of the same insight in one session is one observation, not two. Independence requires different sessions or different sources.

**Appending instead of replacing.** State and Top of Mind get REPLACED each session. Recent Context gets REWRITTEN (not appended). Appending without replacing causes drift toward bloat-instead-of-smart.

**Compression-as-performance.** Restating the same content with fewer words but same surface area is not compression. Real compression: pattern graduation (3 surface facts → 1 meta-pattern) + temporal cleanup (stale → archive) + format density.

**Skipping active demotion.** Patterns that no longer hold get to stay because demoting is uncomfortable. Result: continuity accumulates ghost patterns. The immune system requires you to demote when evidence demands it — and a wrap that produces a SHRINKING continuity is the system working correctly, not failing.

## Reference

See [REFERENCE.md](./REFERENCE.md) for: complete FlowScript marker spec with examples, deeper compression guardrails (research-validated against ~30% persona drift risk per Li et al. 2024), file size budgets and pre-commit enforcement patterns, bilateral cross-substrate integration patterns (if running multi-agent setups), and the philosophical foundation — Complementary Learning Systems (CLS) model from cognitive science that this protocol operationalizes via the hippocampus (episodic store) → neocortex (continuity file) consolidation step.
