# flow-consultation-dispatch — Reference

Deep-dive content for the multi-AI consultation pattern. Generalized
from production use in FLOW methodology, cross-validated by Tony
Sturnus's independently-derived implementation in production lead-gen
agent work (May 2026).

---

## State machine — complete specification

A consultation moves through five explicit states. Each state has
preconditions and permitted transitions.

```
┌──────────────┐    ┌─────────────────────┐    ┌──────────────┐
│  collecting  │───▶│ ready_for_synthesis │───▶│ synthesized  │
└──────────────┘    └─────────────────────┘    └──────────────┘
                                                       │
                                                       ▼
                                              ┌──────────────┐
                                              │   decided    │
                                              └──────────────┘
                                                       │
                                                       ▼
                                              ┌──────────────┐
                                              │    closed    │
                                              └──────────────┘
```

### State definitions

**collecting** — at least one agent has been dispatched; at least one
agent has not yet returned. Transition out: when all dispatched agents
have written their responses.

**ready_for_synthesis** — all dispatched agents have returned. No
further data collection is in flight. The synthesizer can begin
reading. Transition out: when synthesis output is produced.

**synthesized** — synthesis output exists in the consultation artifact.
The decision-maker can now read both raw agent responses AND
synthesis. Transition out: when decision is made.

**decided** — a decision has been recorded with rationale. The
consultation has produced its action. Transition out: when the
artifact is marked complete (typically after downstream work begins).

**closed** — consultation artifact is complete and read-only as far
as this consultation is concerned. Stays as audit trail; searchable
when similar questions arise later.

### Why the state machine matters

Without explicit states, you get:

- Pre-synthesizing on partial input (collapses to single-substrate)
- Synthesizing without all agents back (anchors on first arrivals)
- Decision-making without synthesis (skips the value layer)
- Re-opening consultations weeks later without context (loses provenance)

The state in the artifact itself (not in the orchestrator's memory)
makes the consultation auditable and resumable across sessions.

---

## review-handoff vs review-process split

Tony Sturnus's contribution: separating the *handoff* (preflight to
ensure inputs are clean) from the *process* (one-shot synthesis).

### review-handoff

Strict preflight before dispatching. Verifies:

- The question/prompt is precise enough to produce orthogonal value
- Relevant context is included (code, prior decisions, constraints)
- Decision authority is named
- Hidden Assumptions discipline is in the prompt
- Each agent's input is well-formed

If any of these fail, the handoff fails — do NOT dispatch yet.

This prevents the most common failure mode: dispatching a vague
question, getting vague responses, and "synthesizing" generic
best-practices noise that adds nothing.

### review-process

One-shot synthesis after all agents return. Designed for closure,
not iteration. The synthesizer:

1. Reads all agent responses in full
2. Produces convergence / divergence / unique-contributions analysis
3. Records decision-OR-deferred-OR-split outcome
4. Updates state machine to `synthesized` then `decided` (or back to
   `collecting` with a sharpened question if deferred)

The split (handoff vs process) is architecturally cleaner than
mixing preflight discipline into the synthesis step. Each side has
one job; failures localize.

### Anti-ping-pong rule

If the consultation reopens (synthesizer wants more input), it goes
through review-handoff again — a SHARPENED handoff with a more
specific question, not the same handoff repeated. Otherwise you ping
back and forth and the substrate degrades into "we already heard
this."

---

## Architectural triangulation

When dispatching multiple rounds of consultation (different questions
on the same underlying decision), later reviewers read prior reviews
to target blind spots — NOT to duplicate prior work.

### Procedure

1. **First-round consultations** dispatch in parallel against the
   initial question. Synthesize.
2. **If gaps remain or new questions surfaced**, dispatch a
   second-round consultation. CRITICAL: the second-round prompt
   includes the first-round synthesis as context.
3. Second-round agents read the first-round synthesis and are
   instructed: "your job is to surface what the first-round analysis
   missed, not to duplicate it."
4. Synthesize second-round findings against first-round synthesis.
   Update decision.

### Why triangulation matters

Without it, second-round consultations re-cover the ground
first-round already covered (waste). With it, each round
*architecturally targets* what prior rounds couldn't see —
geometric, not additive, coverage growth.

### Example

First round: "Should we use Postgres or Redis for sessions?"
- 3 agents consult, synthesis converges on Postgres with caveats around
  high-concurrency scenarios.

Second round: "Given we're going with Postgres for sessions per
[first-round synthesis], what are we likely to miss about
high-concurrency edge cases?"
- 3 agents triangulate on what the Postgres path under-explores.
  Surfaces specific load patterns to test for, connection-pool
  configuration risks, and a fallback design.

The second round is impossible without first-round synthesis as
context. The geometric coverage is the architecture.

---

## Agent selection heuristics by review class

Different review classes have different agent-mix optima. These are
production-validated defaults — adjust based on your specific
substrate availability.

### Code review

Best mix: adversarial code reviewer (e.g., Claude with code-review
prompt) + cross-substrate (Codex / Gemini) + structural reviewer
(focused on architecture not line-by-line).

Catches: line-by-line bugs (adversarial), different bug classes
(cross-substrate has different training distributions for code
patterns), and architectural concerns (structural reviewer).

### Architecture / design review

Best mix: adversarial + cross-substrate + frontier-scout (someone
asking "does this already exist?" / "is this re-inventing X?") +
synthesis-curator.

Catches: holes in the proposed architecture (adversarial), training-
distribution-specific blind spots (cross-substrate), prior art
(frontier-scout), and the integrative read (curator).

### README / docs / positioning review

Best mix: adversarial + cross-substrate + narrative-integrity reviewer
(asking "does this tell an honest story?").

Catches: claims not supported by content (adversarial),
training-distribution-different audience-readiness reads
(cross-substrate), positioning that mismatches the underlying truth
(narrative integrity).

### Strategic / market / positioning review

Best mix: adversarial-strategic (positioning, market thesis,
competitive assumptions) + cross-substrate + frontier-scout
(landscape intelligence).

Catches: weak strategic assumptions (adversarial-strategic), priors
the dominant substrate is anchoring on (cross-substrate), and what
the market signal actually says (frontier-scout).

### Essay / publication review

Best mix: adversarial + cross-substrate + narrative-integrity +
discourse-aware (someone tracking the conversation the essay enters).

Catches: weak arguments (adversarial), tone/register issues
(cross-substrate), narrative coherence (integrity), and timing/
positioning relative to ongoing discourse (discourse-aware).

---

## Hidden Assumptions section template

Drop this verbatim into your consultation prompt for each agent:

```
Before your analysis, complete this section in your response:

## Hidden Assumptions

List 3-5 assumptions you are making about this question that, if
wrong, would materially change your answer. For each assumption,
state:

1. The assumption itself
2. Why it matters — specifically, how your answer would shift if the
   assumption were false
3. Whether the question's framing forces or merely permits the
   assumption

Examples of useful hidden assumptions:
- "I'm assuming the team has 5-10 engineers" (team-size assumption
  shifts complexity tolerance)
- "I'm assuming this is greenfield" (existing-architecture
  assumption shifts cost calculus)
- "I'm assuming you're optimizing for time-to-ship" (priority
  assumption shifts quality vs. velocity tradeoff)

Avoid surface-level "I assume good faith" or "I assume you read the
docs" — those don't shift answers. Surface the load-bearing
assumptions.

After Hidden Assumptions, proceed with your analysis.
```

When synthesizing, compare Hidden Assumptions sections across agents
explicitly. The patterns of agreement and disagreement at the
assumption layer often explain the patterns at the recommendation
layer.

---

## Review intent collision rules

When a consultation finding conflicts with a prior decision recorded
in the project's decisions log, default behavior is **challenge the
finding**, not silently accept it.

### Procedure

1. Synthesizer reads the consultation finding and notes the conflict
   with prior decision `[decided(rationale, on)]`
2. Synthesizer asks: did the consultation surface NEW evidence the
   prior decision didn't have?
   - YES → finding may warrant re-opening the prior decision. Surface
     explicitly: "this consultation finding contradicts decision X
     from [date]. New evidence: Y. Recommend re-opening?"
   - NO → finding is reiterating concerns the prior decision already
     weighed and dismissed. Note in synthesis: "consultation surfaced
     concern Z; this concern was considered in decision X and resolved
     for [reason in prior decision]. No new evidence."

### Why this matters

Without this rule, every consultation can re-litigate every prior
decision. Decisions get stuck in perpetual review. With this rule,
prior decisions are respected unless consultations surface
genuinely new evidence — the bar is the decision substrate, not the
consultation noise floor.

### The rule in shorthand

> Findings that conflict with prior decisions require new evidence
> to overturn them. Reopening = OK. Re-litigating = NO.

---

## Consultation file format

Recommended structure (markdown, version-controlled):

```markdown
# Consultation: [short title]

**Status:** collecting | ready_for_synthesis | synthesized | decided | closed
**Question:** [the precise framed question]
**Dispatched:** YYYY-MM-DD HH:MM
**Decision authority:** [who decides]

---

## Context

[Relevant code, prior decisions, constraints, what's been tried]

---

## Agent: [agent-1-name]

**Returned:** YYYY-MM-DD HH:MM

### Hidden Assumptions

1. ...
2. ...
3. ...

### Analysis

[Agent 1's full analysis]

### Recommendation

[Agent 1's recommendation]

---

## Agent: [agent-2-name]

**Returned:** YYYY-MM-DD HH:MM

[Same structure]

---

## Agent: [agent-3-name]

**Returned:** YYYY-MM-DD HH:MM

[Same structure]

---

## Synthesis

**Synthesizer:** [human name or designated synthesis role]
**Produced:** YYYY-MM-DD HH:MM

### Convergence

[Where the agents agreed and what that signals]

### Divergence

[Where they disagreed and what's investigable]

### Unique contributions

[Single-agent insights worth preserving]

### Hidden Assumptions audit

[What assumptions emerged in common; what does that say about the
prompt frame]

### Recommended action

[Synthesized direction]

---

## Decision

**Decided:** YYYY-MM-DD
**By:** [decision-maker]
**Decision:** [the call]
**Rationale:** [why]
**Linked artifact:** [where the decision is recorded for the project]
```

The file itself is the audit trail. State transitions update the
**Status:** field at the top.

---

## Anti-patterns deep dive

### Letting agents see each other before drafting

Most common failure. If agent B reads agent A's output before
drafting, B's hidden-assumptions-surface gets contaminated by A's
framing. The independence that makes convergence informative is lost.

Mitigation: dispatch all agents from the orchestrator in parallel.
If manually coordinating, copy the prompt into all surfaces *before*
reading any responses. Discipline matters here.

### Automating the synthesis

Tempting because the orchestrator can read all the responses
mechanically. But the synthesizer's judgment about *which divergence
is investigable signal vs. taste-level disagreement* is the
architectural value. A naive automated synthesizer averages, which
destroys divergence — the most valuable signal.

Mitigation: explicit human-in-loop synthesis step. Or, if scaling
beyond what a human can do, design a curator-agent role distinct
from the consulting agents, with explicit instructions to NOT
average but to identify investigable divergence.

### Single-substrate echo chamber

Calling the same model 3 times at different temperatures is NOT
cross-substrate consultation. The training distribution is the same;
the blind spots are the same; you've gained nothing except cost.

Mitigation: use different model families (Claude / GPT / Gemini), or
different organizations entirely. Different training distributions
is the architectural premise.

### Majority voting on divergence

"2 of 3 said X, so X wins." This destroys the divergence that was
the whole point. The dissenter often has the most valuable finding
*because* they're divergent — their training distribution sees what
the others' don't.

Mitigation: read divergence as signal, not noise. Investigate the
dissenter's rationale before discounting it.

### Consultation as validation theater

If you already know the answer and dispatch a consultation to feel
better about it, the consultation either confirms what you wanted
(no new information) or surfaces divergence you rationalize away
(no integrity).

Mitigation: skip the consultation. Just commit. Use consultation
when there's a real question.

### Synthesis on partial input

Don't begin drafting synthesis until all dispatched agents have
returned. Pre-synthesis anchors your reading of later responses on
the early arrivals.

Mitigation: state machine. `ready_for_synthesis` is a real state
transition; respect it.

---

## Provenance

This pattern was developed in FLOW methodology starting late 2025,
formalized via `consult.py` (multi-AI consultation orchestrator) +
the four-agent consultation team (Complement / Gemini / Codex /
Contrarian for code; +Anansi/Daemon/Diogenes for other classes).

Cross-validated in production by Tony Sturnus's independently-derived
implementation in Paperclip-based lead-gen agent work (May 2026):
`/consult` + `/consult-add` + `/consult-process` skills derived from
the same architectural principles. Tony's contributions to the
formalization above include:

- **Hidden Assumptions section** as a per-agent discipline (was
  implicit in the FLOW version; Tony made it structural)
- **State machine inline in the consultation artifact** (FLOW's version
  was ephemeral in the orchestrator; Tony made it auditable in the
  file itself)
- **review-handoff vs review-process split** (FLOW's version mixed
  preflight into synthesis; Tony separated them)
- **Role-based processor authority** (next.md AI Team Roles —
  designated synthesis role)
- **review_intent_collision rule** (challenge findings that conflict
  with prior decisions; default to NOT re-litigating)
- **Architectural triangulation rule** (later reviewers read prior
  reviews to target blind spots, not duplicate them)

Operator-class independently-derived patterns at this depth are the
strongest validation signal for the underlying architecture.
