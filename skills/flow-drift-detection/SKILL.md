---
name: flow-drift-detection
description: Persistent agent state (continuity files, project memory, identity documents, accumulated decisions, "facts" sections) drifts from reality over time as the world changes and old assertions go stale. Use this skill to dispatch a fresh-context adversarial reviewer against accumulated state with explicit reality-check questions, surface stale facts and outdated claims, then archive or update what's drifted. Distinct from cold-read (which reviews fresh drafts pre-commit) — drift-detection reviews STORED state that's been accumulating. Run on a cadence (weekly / monthly), after significant external state changes, or when "facts that were true 3 months ago" feel suspect. Triggers include scheduled drift-sweep, "audit the continuity", "what's stale in my project memory", "are my facts still true", or noticing a decision from N months ago is being acted on without re-verification.
---

# flow-drift-detection

Adversarial review of accumulated state catches when continuity has drifted from reality. Companion to `flow-cold-read`, but applied to PERSISTENT state rather than fresh drafts. Memory files accumulate; the world changes; assertions go stale. Without scheduled drift-detection, agents act on facts that were true months ago.

## When to use

Trigger conditions:
- Scheduled cadence (weekly for high-velocity work, monthly for slower-changing state)
- After significant external state changes (vendor announces breaking change, library has major version, organizational restructure)
- When acting on a decision or fact from N months ago without recent verification
- User asks "audit the continuity", "what's stale", "are my facts still true"
- After extended absence (weeks/months) when returning to a project
- Before major commits that depend on accumulated state's accuracy

Do NOT use for:
- Fresh drafts pre-commit (use [`flow-cold-read`](../flow-cold-read/SKILL.md) instead)
- Individual claim verification (use [`flow-web-verify-before-claiming`](../flow-web-verify-before-claiming/SKILL.md) instead)
- Stable state that hasn't been touched (no drift if no accumulation)
- Reactive crisis ("something broke; check the docs") — that's debugging, not drift-detection

## What this skill assumes

This skill assumes the agent has:
- **Persistent state files** to audit (continuity files, project memory, decision logs, identity documents)
- **A fresh-context reviewer** — either a different AI substrate, a new session of the same substrate without prior context loaded, or a human reviewer. The "fresh" matters — same-session-same-context-same-substrate cannot see what same-context cannot see.
- **Web search access** (or equivalent reality-check mechanism) for the reviewer to verify facts against current world state

## The drift-detection procedure

### Step 1: Identify what to audit

State that drifts most:
- **Action Items** — tasks complete naturally; staleness sneaks in via uncompleted-but-no-longer-relevant items
- **Top of Mind** — cognitive salience layer; if items have been "top of mind" for 30+ days unaddressed, are they actually top of mind?
- **Developing Knowledge with `>7 days stale`** — temporal cleanup should have caught these; drift-detection catches what cleanup missed
- **Proven patterns with external dependencies** — patterns about external tools/services/people that may have changed
- **Decision logs** — old `[decided(rationale, on)]` entries; is the rationale still operative?
- **"Facts" sections** — anything declarative about external world (library versions, API surfaces, organizational state, market conditions)

State that drifts least:
- **Foundation / identity / partnership protocol** — near-permanent truths, infrequent legitimate change
- **Architecture patterns proven via 9+ instances** — methodology layer, slow-changing
- **Personal preferences** — your own style, voice, working patterns

Prioritize the high-drift surfaces. Don't audit everything every time — that's expensive and most of it doesn't drift.

### Step 2: Frame the audit prompt

Effective drift-audit prompts ask the reviewer to challenge state against reality, not to read sympathetically. Template:

```
Below is [accumulated state content].

This was written [date or time-range].

Audit for drift against current reality. For each section, ask:

1. Is anything here factually wrong NOW even though it was right when written?
2. Are there assertions about external state (tools, APIs, services, organizations, people, market) that need re-verification?
3. Are there decisions whose rationale no longer holds under current conditions?
4. Are there items marked "active" / "in progress" / "top of mind" that are actually dormant?
5. Are there `[parking]` items past their `until` date that should be reconsidered?

Use web search to verify external facts. Surface specific items with:
- The original claim or item
- What's drifted (or "still current" if verified)
- Recommended action: update, archive, demote, or leave

Be explicit and direct. Don't read sympathetically — read adversarially.
Stale state acted on is more dangerous than gaps in state.
```

### Step 3: Dispatch + review findings

Send the prompt + state to the fresh-context reviewer. Review what comes back.

The reviewer should produce a structured report:
- Items still current — no action
- Items drifted — what's stale + recommended update
- Items dormant despite "active" framing — should archive or revive intentionally
- Items past `until` date — should reconsider explicitly
- Items requiring further verification — flagged but reviewer couldn't resolve

### Step 4: Act on findings (the actual update)

For each drifted item:

- **Update** — fact has changed; revise the state file with current accurate fact + date of verification
- **Archive** — item is no longer relevant; remove from active state (git history preserves it)
- **Demote** — pattern that was Proven but evidence no longer supports; demote to Developing (or remove entirely if evidence has been falsified)
- **Re-decide** — past `[decided]` whose rationale doesn't hold; either re-affirm with current rationale or change the decision with new `[decided(rationale, on)]` entry

Commit the updates with explicit drift-detection commit message: "Drift audit [date]: updated 4 items, archived 2, demoted 1."

### Step 5: Schedule next audit

If this was a scheduled drift-sweep, mark next sweep date in the appropriate task surface. Common cadences:

- **Weekly:** for high-velocity projects with lots of external dependencies (vendor APIs, libraries, market state)
- **Monthly:** for slower-velocity work; standard for most projects
- **Quarterly:** for slow-changing identity/methodology surfaces
- **Event-triggered:** after major external state change (vendor announcement, organizational shift, breaking change in dependency)

## Drift categories — what to watch for

### External state drift (highest frequency)

External facts that change without notifying you:
- Library / framework versions and feature availability
- API surfaces (parameters, response shapes, deprecation)
- Service pricing and limits
- Third-party tool features (the AI ecosystem changes weekly)
- Organizational state (who runs what, what's been acquired, what's been shut down)
- Market conditions and competitive landscape

These need real verification (web search, official sources). The reviewer cannot resolve from accumulated context alone.

### Reframing drift (medium frequency)

Internal facts that haven't changed but your understanding of them has. Examples:
- A pattern that was named one way and is now better-named another (operationalize → operational; substrate → infrastructure)
- A decision whose rationale was articulated one way and the current sharper articulation supersedes it
- An assumption that was load-bearing and is now provably weaker

These need cognitive review, not web search. The fresh-context reviewer's framing-distance from the original state is the diagnostic surface.

### Dormancy drift (medium frequency)

Items marked active that have been silently dormant. The "active threads" stagnation pattern:
- Task on Top of Mind for 60+ days untouched
- "In progress" project with no commits in months
- "Blocked since" date from 6 months ago

Either revive intentionally (with explicit rationale for why now) or archive intentionally (with rationale for why we're not doing this). The middle state — "still on the list but not happening" — corrodes the entire active-state surface.

### Decision-rationale drift (low frequency, high impact)

Old decisions whose rationale was sound at the time but doesn't hold under current conditions:
- "Use X library because Y constraint" — Y constraint has since lifted
- "Don't do Z because of A risk" — A risk has since been mitigated by external factor
- "Ship via approach B because of C tradeoff" — C tradeoff has shifted

These are highest-impact because acting on stale decisions compounds error. The audit should explicitly re-examine `[decided]` entries older than ~3 months for whether the rationale still holds.

## Common mistakes

**Same-context drift audit.** Auditing your own state from inside the same session that produced it cannot see what same-context cannot see. The "fresh" in fresh-context reviewer is load-bearing. Use a different substrate, a new session, or a human partner.

**Audit theater.** Running a drift-audit prompt, getting findings, then not acting on them. The audit's value is in the UPDATE step. If you're not committing changes from drift-detection, you're performing audits, not detecting drift.

**Verifying internally.** "I think X is still right" without web-checking. Same trained-default fabrication pattern as [`flow-web-verify-before-claiming`](../flow-web-verify-before-claiming/SKILL.md) — the moment you feel confident X hasn't changed is often when X has changed.

**Auditing everything every time.** Don't audit Foundation / identity / partnership protocol weekly — they don't drift that fast. Prioritize high-drift surfaces (external state, Action Items, Top of Mind). Different cadences for different sections.

**Sympathetic reading.** Adversarial review surfaces drift; sympathetic review confirms current beliefs. The audit prompt should explicitly instruct adversarial framing. "Audit this kindly" is the wrong prompt.

**Skipping the schedule.** Drift-detection IS the scheduled cadence. If you only run when you suspect drift, you're already late (drift you noticed is drift that's already affected work). The cadence is the structural mechanism.

## Drift-detection IS the scheduled cadence

This skill's architectural value is in the SCHEDULED firing, not in the on-demand firing. Without a schedule:

- Drift accumulates until something visibly breaks
- The break point is much later than when drift first started
- Backtracking from broken-state is more expensive than catching at first drift

With a schedule:

- Each audit catches a window of drift
- The window is bounded
- Backtracking from each audit is small

The cadence (weekly/monthly/whatever fits the velocity) is the structural defense. The skill provides the procedure; the schedule provides the structure.

## Reference

This skill is self-contained — mechanics are procedural rather than mechanically deep. Related skills:

- [`flow-cold-read`](../flow-cold-read/SKILL.md) — cross-substrate evaluation for FRESH drafts (this skill: ACCUMULATED state)
- [`flow-web-verify-before-claiming`](../flow-web-verify-before-claiming/SKILL.md) — verification at the claim boundary (this skill: scheduled audit of accumulated claims)
- [`flow-session-wrap`](../flow-session-wrap/SKILL.md) — includes temporal cleanup which catches some drift; drift-detection catches what temporal cleanup misses (semantic drift, not just temporal staleness)
- [`flow-forever-rules`](../flow-forever-rules/SKILL.md) — declared invariants that should NOT drift; drift-detection should explicitly NOT flag these even if they appear stale

## Provenance

This pattern is one of FLOW methodology's anti-fragility operations (Op 21 from the [Three Layers Underneath Agent Orchestration](https://nemooperans.com/three-layers-underneath-agent-orchestration) paper). In FLOW's production use, drift-detection is operationalized as scheduled adversarial review against accumulated continuity (run by Diogenes consultation agent on weekly cadence). The pattern surfaces drift FLOW's own session-wrap temporal cleanup couldn't catch — semantic drift, decision-rationale drift, and external-state drift specifically.

The architecturally important insight: **scheduled adversarial review is a structural defense against the gradual accumulation that the operator running the system cannot see from inside loaded context.** Same family as cold-read; different surface (state vs. drafts) and different cadence (scheduled vs. boundary-triggered).
