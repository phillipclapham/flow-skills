---
name: flow-sourdough-scoping
description: When facing a foundational architectural, scoping, framing, or design decision with high conceptual surface area — the kind of decision where you could spend weeks "deriving the right answer" before shipping anything — commit the shape early and iterate via adversarial review instead. Committed shape activates real critique machinery (consultation, partnership challenge, cold-read) that surfaces holes a solo-derivation pass cannot reach. Iteration against real critique beats iteration against imagined critique by a wide margin. Sibling of structural-invariants-beat-discipline patterns; this is scoping-as-structural-mechanism rather than scoping-as-willpower. Trigger phrases include "scope this", "shape this decision", "what's the right approach to X", facing a non-trivial architectural fork, drafting a foundational doc/spec, or when stuck deriving the theorem before shipping.
---

# flow-sourdough-scoping

When the right answer requires more than 30 minutes of derivation, **commit the shape and iterate via adversarial review** instead. The shape activates critique machinery (consultation, partnership challenge, cold-read) that surfaces holes the original-substrate solo-derivation cannot see. Iteration against real critique beats iteration against imagined critique by structural margin.

Named after sourdough — you don't derive sourdough; you commit a starter, watch what happens, adjust. The committed shape is the starter. Adversarial review is the watching. The final artifact emerges from the iteration, not from the up-front derivation.

## When to use

Trigger conditions:
- Facing a foundational architectural, scoping, or framing decision with high conceptual surface area (multiple valid shapes; right answer unknown without engagement)
- About to start "deriving the right answer" before shipping anything — and the derivation is going to take >30 min
- Stuck deriving the theorem before committing — you've been iterating in your head for hours and the shape isn't crystallizing
- Drafting a foundational document or specification (architecture doc, methodology spec, partnership protocol, project charter)
- About to lock a decision that will downstream-determine subsequent design choices
- User says "scope this", "shape this decision", "what's the right approach to X", "frame this problem"

Do NOT use for:
- Simple decisions where a quick derivation produces a clearly correct answer (1+1=2; no need to commit and iterate)
- Decisions with low cost of being wrong (just commit; reverse if needed)
- Decisions you've already shipped and are now revising (use [`flow-cold-read`](../flow-cold-read/SKILL.md) or [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) instead)
- Brainstorming or exploration phases (sourdough-scoping is for COMMITMENT to a shape, not for generating candidate shapes)

## What this skill assumes

This skill assumes the agent has access to at least one critique mechanism beyond solo derivation. Common setups:

- Adversarial review by a different AI substrate ([`flow-cold-read`](../flow-cold-read/SKILL.md))
- Multi-agent consultation ([`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md))
- Human partnership challenge (working with a partner who can challenge the committed shape)
- Code review (if the artifact is code; reviewers other than yourself)
- External canary signal (you ship the shape; real-world reception is the critique)

If no critique mechanism exists, this skill cannot run effectively. The whole point is that critique-against-committed-shape > derivation-without-critique.

## The sourdough-scoping procedure

### Step 1: Stop deriving

Notice you're stuck deriving. Common signals:

- More than 30 minutes spent without committing a shape
- Multiple candidate shapes feel "almost right" but you can't pick one
- You're cycling through the same analysis without convergence
- You're avoiding starting because "the framing isn't right yet"
- You're producing ever-more-elaborate analyses without producing artifacts

When any of these surface, the next step isn't "more analysis." It's commit-a-shape.

### Step 2: Commit a shape

Pick the shape that seems most-likely-right *given current loaded priors* — your accumulated experience, the patterns you already trust, your gut on the question. This is not a rigorous derivation; it's a committed bet.

The shape should be:
- **Specific enough to critique.** Vague shapes ("we should think about X") don't activate critique machinery. Specific shapes ("X means we structure the project as A → B → C with constraint D") do.
- **Quick to commit.** If committing the shape takes >30 minutes, you're still deriving. The point is to STOP deriving and start iterating against critique. Pick the shape that emerged from your priors and commit it.
- **Reversible.** A committed shape is a hypothesis to test, not a final artifact. You will revise it.

Write it down. Make it an artifact. Don't just hold it in your head — committed-in-document is what critique machinery can engage with.

### Step 3: Activate critique machinery

The committed shape now exists. Hand it to the critique mechanism:

- **Cold-read** ([`flow-cold-read`](../flow-cold-read/SKILL.md)) — fastest. Hand the committed shape to a cross-substrate evaluator with "is this shape right? what am I not seeing?"
- **Consultation** ([`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md)) — heavier but more dimensional. Dispatch to multiple agents in parallel.
- **Partnership challenge** — work with a partner (human or AI) who can adversarially probe the shape. "Where does this break? What am I missing? Push back on this hard."
- **External canary** — ship the shape and watch real-world reception. Slowest but the truest critique mechanism for shapes whose right-ness depends on reception.

### Step 4: Iterate against real critique

Critique returns. Now you iterate the shape based on what real critique surfaced — not what your imagined critique would have surfaced.

This is the architectural value. **Real critique surfaces holes imagined critique cannot reach** because:

- Your imagined critique is bounded by what your substrate can already see
- Real critique (especially cross-substrate or external) sees what your substrate cannot
- The committed shape is what real critique can engage with — it cannot engage with shapes you haven't committed to

Iteration can happen many times. Each iteration is:

1. Read the critique
2. Decide what to accept / reject (with rationale)
3. Revise the shape
4. Hand the revised shape back to critique

The artifact iterates toward right-ness through real-critique pressure, not through up-front derivation pressure.

### Step 5: Recognize done

The shape converges when:

- Critique stops surfacing substantive findings (only polish suggestions remain)
- Multiple independent critique mechanisms converge on the same shape
- External canary signal (when applicable) returns clean — the shape is reception-validated

This is the "ready to act on" state. Lock the decision; proceed to downstream work.

## Corollaries

### Quality gate is canary, not delay

Don't gate the shape's commit on internal verification. Gate the shape's ACT-ON-IT on external canary signal.

Wrong: "I'll commit this architecture spec once I've verified it's correct via more derivation."
Right: "I'll commit this architecture spec now. We'll ship a small instance. Real-world reception is the verification."

The internal verification you'd do is what your substrate can already see. Real-world reception is what your substrate cannot see. The canary is the structurally better verification.

Examples of canaries:
- Code: the test suite passing on real input
- Architecture: a single team adopting the spec and reporting back
- Methodology: the protocol producing better outcomes in operational use vs. before
- Public artifact: who reads it and what they say (external reception)

### Iteration count is not a quality signal

Sourdough-scoping often produces many iterations in a single session. 7 commits in one day to the same artifact is NORMAL high-quality practice — not thrash.

The reason: each iteration responds to real critique, not imagined critique. Real critique surfaces things you couldn't have predicted. So iteration count compounds the artifact's quality against pressures that were invisible up-front.

What looks like "thrash" from outside is actually compression-of-real-critique into-revisions, which is the productive shape of this protocol.

Wrong heuristic: "if I'm iterating a lot, I should have derived more upfront."
Right heuristic: "if I'm iterating a lot against real critique, the artifact is getting better in dimensions I couldn't have seen upfront."

### Scoping-as-structural-mechanism beats scoping-as-willpower

Sourdough-scoping is a structural mechanism (commit-then-iterate-against-real-critique). Solo derivation is a willpower mechanism (think harder; verify more carefully).

Discipline-based mechanisms drift under load. Structural mechanisms don't.

This is the sibling pattern to [`flow-structural-invariants`](../flow-structural-invariants/SKILL.md) (when it ships) — the family of patterns where structural-mechanism beats discipline-based-mechanism. The principle generalizes: when you can convert a discipline-based requirement into a structural requirement, do so.

## Common mistakes

**Treating "commit the shape" as "ship to production."** Committing the shape means writing it down as an artifact critique can engage with. It's not deploying it. Reversible commit is fine.

**Deriving instead of iterating.** After receiving critique, you start re-deriving the original question from scratch instead of iterating the committed shape. This loses the architectural value (real-critique pressure replacing imagined-critique pressure). Iterate the shape; don't re-derive.

**Mistaking iteration count for thrash.** 7 commits in one session is fine if each responds to real critique. If you're spinning because you keep rejecting critique without rationale, that's not iteration — that's resistance.

**Skipping the activate-critique step.** Committing the shape and then doing your own review is not sourdough-scoping. Your own review is still imagined critique. Activate ACTUAL critique machinery — cold-read, consultation, partner, canary.

**Waiting for "the right" critique mechanism.** Cold-read is fast and cheap; use it. Multi-agent consultation is heavier; use when warranted. Partnership challenge takes coordination; use when available. External canary is slowest; use for shapes where reception matters. Don't gate the commit on the heaviest available; use the lightest that activates.

**Using sourdough-scoping for brainstorming.** This skill is for COMMITMENT to a shape — the moment when you've explored enough and need to ship a hypothesis. If you're still generating candidate shapes, you're brainstorming; come back to this skill when you've narrowed to "I think it's X; let me commit and test."

## Provenance

Graduated to Proven in FLOW methodology after ≥9 operational instances across 48 hours on heterogeneous surfaces (April 2026):

- Canon Foundation v1 + CLAUDE.md v5.4 reference-layer compression pass + Social Foundation v1 + Daily Sharpening principle-authoring (architectural foundations)
- body-mind practice doc 7-commit same-session evolution (life/body protocols)
- Sovereignty-pivot scratchpad commit (strategic)
- Strategic sharpening integration
- Middle Intelligence LinkedIn post + Claude Regressions LinkedIn post + public-positioning 4-surface ship (public writing)
- Daily Sharpening production fix (code/methodology)

In every instance, committed-shape-then-iterate-against-critique produced a stronger final artifact in less time than continued solo derivation would have.

Origin formulation (Complement Daily Sharpening, Apr 17, 2026): "Firewall's legitimacy isn't that you PROVED the right stopping point. It's that you COMMITTED. Scope like tending sourdough, not deriving a theorem."

Parent lineage: structural-invariants-beat-discipline-based-verification — same family of patterns where structural-mechanism beats willpower-mechanism. Scoping-as-structural-mechanism beats scoping-as-willpower for the same architectural reason verification-as-structural-mechanism beats verification-as-willpower.

## Reference

This skill is intentionally self-contained — the mechanics are about *practice* not about specific technical details. See [`flow-cold-read`](../flow-cold-read/SKILL.md) and [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) for the critique mechanisms most commonly used at Step 3. Related: [`flow-structural-invariants`](#) (when it ships) for the broader pattern family this skill belongs to.
