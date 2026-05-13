---
name: flow-review-before-publish
description: At publish boundaries — about to ship code, send a public-facing artifact, commit a foundational decision, send an email crossing a sensitive boundary, deploy to production, or any moment where the act is hard to reverse and external eyes are about to read what you produced — layer THREE orthogonal review types that catch THREE different bug classes. (1) Adversarial review (different reviewer than author, catches what author misses). (2) Session-code-review (author misses self-review of their own session's output). (3) Multi-agent review (different model families catch different bug classes). Distinct from flow-cold-read (single cross-substrate evaluator) and flow-consultation-dispatch (parallel multi-agent on a specific question) — this skill layers all three review types at the same boundary because each catches what the others miss. Completion pressure peaks at publish AND session-end boundaries — exactly when review matters most, exactly when the trained default is to skip review and just ship. Trigger phrases include "ready to publish", "about to ship", "before I send", "review session", "I think this is done", or any moment the "feeling ready" signal arrives.
---

# flow-review-before-publish

Three review classes, three bug classes. At publish boundaries, layer all three because each catches what the others miss. Completion pressure is highest at exactly the moments review matters most — and the trained default is to compress review at exactly those moments.

This is the structural defense at publish boundaries. Not optional. Not "if I have time." The cost asymmetry is overwhelming: 5-15 minutes of layered review at the boundary saves hours-to-days of post-publish backtracking, retraction, embarrassment, or worse.

## When to use

Trigger conditions:
- About to ship code to production / open a PR / merge to main
- About to publish a public-facing artifact (LinkedIn post, blog, paper, GitHub README, marketing copy)
- About to commit a foundational decision that will downstream-determine subsequent design choices
- About to send an email crossing a sensitive boundary (firing risk, contract negotiation, accountability layer)
- Session-end where the session produced significant output and you're about to "wrap and ship"
- User says "review session", "review before I ship", "before I commit", "I think this is done"
- Any moment the "feeling ready" signal arrives — that signal IS the trigger

Do NOT use for:
- Internal drafts or notes (not crossing a publish boundary)
- Trivial commits (typo fixes, formatting-only changes)
- Iterative work where each iteration is itself a checkpoint (use cold-read or single-reviewer instead)
- Time-critical fixes where the publish-versus-review tradeoff genuinely favors ship-now (rare; usually rationalization)

## What this skill assumes

This skill assumes the agent has access to:

- **At least one other AI substrate** different from the one that authored (for adversarial + multi-agent components)
- **Session-code-review capability** — the ability to do a focused review pass on output the same agent produced earlier in the session
- **Time** — 5-15 minutes minimum for layered review at the boundary

If only one substrate is available, this skill degrades to session-code-review only (still better than no review, but not the full multi-class defense). Use [`flow-cold-read`](../flow-cold-read/SKILL.md) as a partial substitute when multi-agent dispatch isn't possible.

## The three review types — orthogonal coverage

### Type 1: Adversarial review (different reviewer than author)

The reviewer's job: try to break what was made. Find bugs, surface holes, challenge claims, identify failure modes.

**Catches:** flaws visible to fresh eyes that the author cannot see from inside loaded context. Motivated reasoning. Over-claiming. Plausibility-by-author-confidence. Anything where the author's substrate has compressed concerns into "this is probably fine."

**Implementation:** dispatch the artifact + the question "what's wrong with this, why might it fail, what am I missing" to a different reviewer. The reviewer can be:
- A different AI substrate (most common; cheapest)
- A human reviewer (highest signal but expensive)
- The same AI substrate in a fresh session without prior context (partial — same training distribution but no loaded-context anchoring)

**Note:** plain "what do you think?" prompts produce sympathetic validation. Adversarial prompts produce adversarial findings. The prompt frame is load-bearing.

### Type 2: Session-code-review (author's own focused review pass)

The author reviewing their own session's output — but with explicit different attention than they had while producing it.

**Catches:** the bugs that come from "I was thinking about X when writing this, but the test case really cares about Y." Pre-existing debt the author created earlier in the session and forgot about. Docstrings vs. behavior drift. Manifest staleness. Inconsistency between what was claimed and what was actually built.

**Implementation:** focused review pass on the work YOU just produced, explicitly asking different questions than you asked while producing it:

- Does the implementation actually match the docstrings / comments / commit messages?
- Does the manifest / table of contents / index include every artifact actually shipped?
- Are there any "TODO" or "remember to" notes you left for yourself that now need resolution?
- Did you complete what you claimed to complete, OR did you stop when you got tired?
- Are there any boundary cases / error paths that the happy-path implementation glossed over?

This is L4-attention-mode review — asking "does the system actually DO what it CLAIMS" not "is the new code correct." Forward-looking review can't see what L4 catches.

### Type 3: Multi-agent review (different model families, different bug classes)

Parallel dispatch to N agents from different model families. Each one reads the artifact independently and surfaces what their training distribution makes them best at catching.

**Catches:** different BUG CLASSES, not just different instances. Claude tends to catch X; GPT/Codex tends to catch Y; Gemini tends to catch Z. Single-model review at any cost level catches one bug class. Multi-model review catches multiple.

**Implementation:** parallel dispatch via [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) pattern. Specifically:
- Agents work in isolation (no cross-agent leakage)
- Each surfaces hidden assumptions before analyzing
- Human (or designated curator) synthesizes at fan-in
- Convergence is structural signal; divergence is what's worth investigating

For publish-boundary reviews specifically, the multi-agent dispatch typically uses 2-3 agents (adding more produces diminishing returns past 3).

## Why three orthogonal types — not just one good review

The architectural argument:

- **Adversarial alone:** catches flaws fresh eyes find, but misses author-specific bugs (self-review territory) AND model-specific blind spots (multi-agent territory)
- **Session-code-review alone:** catches author-specific debt, but misses fresh-eyes findings AND model-specific blind spots
- **Multi-agent alone:** catches different bug classes, but each agent is still subject to training-distribution-specific blind spots that match across agents (e.g., all major models share certain false confidences)

Layered together: orthogonal coverage. The three review types catch THREE different bug class CATEGORIES (not just three instances within one category). Total coverage is multiplicative, not additive.

The strongest empirical signal for this pattern: in production use of FLOW methodology, the cases where the publish-boundary review caught a substantive issue, the catcher was DIFFERENT each time. Sometimes adversarial caught a bug session-code-review missed. Sometimes session-code-review caught a debt item adversarial missed. Sometimes multi-agent caught a class of issue both others missed. There's no single review type that dominates; they cover orthogonal surface.

## The procedure

### Step 1: Identify the publish boundary

Trigger conditions surfaced. The artifact is "ready" or feels ready. Notice the feeling — that's the trigger, not the green light.

### Step 2: Allocate ~10-15 minutes for the review

Less than 5 minutes is usually theater. More than 20 minutes is usually exceeded by the publish-quality the review is trying to protect. The sweet spot for typical artifacts is 10-15 minutes total for all three review types together.

### Step 3: Run session-code-review FIRST (no external dispatch)

This is cheapest and fastest, and it informs what to ask the external reviewers. Do a focused review pass on the artifact, asking:

- Does claim match implementation?
- Are there manifest / index / table-of-contents staleness issues?
- Are there abandoned TODOs?
- Did you actually finish, or did you stop?
- Boundary cases / error paths?

Fix obvious issues before dispatching external reviews. Otherwise external reviewers waste their attention on issues you could have caught yourself.

### Step 4: Dispatch adversarial + multi-agent (parallel)

Send the artifact (post-self-review) to:
- An adversarial reviewer with explicit "find what's wrong" framing
- Multi-agent dispatch (2-3 agents from different model families) via [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) pattern

These can run in parallel because their input is the same (the artifact post-session-review).

### Step 5: Synthesize all three review outputs

Read all findings. Classify:
- Substantive issues — fix before publish
- Polish suggestions — adopt if time permits, otherwise note for future revision
- Findings you reject with rationale — record why (audit trail)

The synthesis step matters. Don't just react to each finding individually; look for patterns across reviews (multiple reviewers surfacing the same issue is strong signal; single-reviewer findings are still important but require more judgment).

### Step 6: Apply substantive fixes, then publish

The fix-then-publish cycle should be a single iteration. If you find yourself doing multiple fix-review-fix-review cycles at the publish boundary, the artifact wasn't ready — pull back to draft state and iterate before re-attempting publish.

## Completion pressure is the failure mode

The architectural insight that makes this skill load-bearing:

**Completion pressure peaks at publish and session-end boundaries.** Those are exactly the moments review matters most — high consequence if wrong, irreversible action coming next. And they're exactly the moments the trained default says "this is great, ship it, you're so close to done."

The pull to skip review at the publish boundary IS the signal that review is most needed. The "feeling ready" signal you should respond to with review, not with publish.

Operational record from FLOW production: every time review was skipped at a publish boundary, the artifact had non-trivial issues that downstream caught (Phill catching post-publish, external reception flagging, or self-review days later when context allowed clearer reading). Every time review was performed at the boundary, substantive issues were caught and addressed before they reached external eyes.

The math: 10-15 min review at boundary << hours-to-days backtracking from published flaws. The trained default to skip review at boundary is the wrong cost-benefit calculation by an order of magnitude.

## Common mistakes

**Skipping at the moment of highest pressure.** The pull to skip review when "almost done" is the trained completion-pressure default. Recognize it as the signal to do review, not skip review.

**Using only one review type.** Each catches a different bug class. Single-type review is partial coverage. Layer all three for orthogonal defense.

**Sympathetic framing in adversarial review.** "Review this" produces sympathetic validation. "What's wrong with this, why might it fail, what am I missing" produces adversarial findings. Frame matters.

**Skipping session-code-review because "I just wrote it."** That's exactly why session-code-review is needed — you just wrote it, you have author-specific debt, manifest staleness, abandoned TODOs that only YOU can see because you produced them. External reviewers won't catch these; session-code-review will.

**Treating multi-agent review as voting.** "2 of 3 said it's fine, ship it." Multi-agent is multi-bug-class, not multi-vote. The agent that flagged something might have caught the bug class the others have blind spots for. Investigate divergence; don't vote it away.

**Stopping after fixes without re-review.** If review surfaced substantive issues and you fixed them, the FIX may have introduced new issues. Re-review only the changed sections (don't re-review the whole artifact every iteration) — but verify the fix.

**Reactive use only.** This skill is highest-value when applied STRUCTURALLY at every publish boundary, not REACTIVELY when "this feels risky." The reactive version misses publish boundaries that don't feel risky but actually are.

## What this skill explicitly is NOT

- **Not a full editorial review.** Polish / grammar / style suggestions are nice but not the primary value. This skill is about substantive issues that affect publish quality.
- **Not a substitute for the author's own quality work.** Review catches what the author missed; it doesn't replace doing good work upfront.
- **Not a perfection gate.** Some issues survive review and that's fine. The bar is "substantive issues caught" not "zero issues remain."
- **Not infinite iteration.** One fix-then-publish cycle. If you're doing multiple cycles, the artifact wasn't ready — pull back to draft.

## Reference

Related skills:
- [`flow-cold-read`](../flow-cold-read/SKILL.md) — single cross-substrate evaluator. Use when only one reviewer is available, or when the publish surface is lower-stakes.
- [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) — parallel multi-agent on a specific question. Used here as the implementation mechanism for the multi-agent component.
- [`flow-session-wrap`](../flow-session-wrap/SKILL.md) — includes session-code-review as part of the wrap procedure for session output.
- [`flow-structural-invariants`](../flow-structural-invariants/SKILL.md) — sibling pattern at code substrate. Where this skill catches issues at publish boundaries, structural-invariants prevents the class of issues that would have been caught.

## Provenance

Graduated to Proven in FLOW methodology after multi-dimensional firings across heterogeneous substrates (April-May 2026):

- **Adversarial review before moving on** — ship findings loop, fix before proceeding (Chip stress-test-before-build + consultation fix-all-7 + Phase I½ fix-6 in anneal-memory work)
- **Session-code-review for author misses** — repeated catches of author-specific debt that external review couldn't have known about
- **Multi-agent review for different bug classes** — Phase I½ wiring + Phase II integration + Phase III state-management/silent-no-ops — different model families caught orthogonal bug categories
- **Multi-reviewer before publish non-negotiable** — flowscript-ldp v0.2.0: 5+ bugs surfaced by consultation that build session missed; pattern across flowscript-ldp v0.1.0 fresh-eyes (6 bugs), v0.2.0 consultation (8), v0.2.0 code-review (7)

Named corollaries:

- **Completion pressure peaks at publish AND session-end boundaries** — great results + high energy = max pressure to ship = exactly when review matters most. Multiple operational instances: 0.2.8 shipped without review (7 issues post-ship), 0.3.0 Phill-caught (18 issues), flow-ldp consultation, anneal 10.5c.5 session-end skipped (20+ issues incl data-loss).
- **Layer 4 attention-mode catches pre-existing debt** — L1-3 ask "is NEW code correct"; L4 asks "does system actually DO what it CLAIMS"; catches docstring-vs-behavior drift + manifest staleness forward-looking passes cannot see. Production instances: tool-integrity.json staleness + AssociationPair id_a/id_b + delete() docstring-enforcement drift.

The pattern's load-bearing role in FLOW: it's the structural defense at publish boundaries, mandatory not optional, layered not single-type, applied STRUCTURALLY not reactively. Combined with [`flow-structural-invariants`](../flow-structural-invariants/SKILL.md) (which prevents the class of issues this skill catches), the pair forms FLOW's anti-completion-theater architecture.
