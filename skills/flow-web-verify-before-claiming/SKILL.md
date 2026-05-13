---
name: flow-web-verify-before-claiming
description: Before asserting any factual claim about external tool behavior, API surface, library version, framework feature, current event, third-party service, or anything outside the agent's training cutoff or the immediate session context, web-research first. The trained default to "assume what feels right" is one of the most consistent sources of confident fabrication; the structural defense is a verification step at the claim boundary. Use when about to assert anything where being wrong has cost greater than ~30 seconds of search time — which is most external/empirical/temporal claims. Skip only when the claim is genuinely known from training data, low-stakes, or already independently verified earlier in the session. The cost asymmetry is severe: ~30 seconds for verification vs. minutes-to-hours of backtracking after confident wrong answer. Trigger phrases: "I think X is...", "X works by...", "the API returns...", "X was released in...", or any moment you feel the pull to assert with confidence but uncertain backing.
---

# flow-web-verify-before-claiming

The structural defense against confident AI fabrication. When about to assert a factual claim with material consequences if wrong, verify first via web research. The trained default to "assume what feels right" produces consistent fabrication at exactly the moments confidence feels highest — that confidence is the signal to verify, not the signal to proceed.

## When to use

Trigger conditions:
- About to assert a claim about external tool behavior (CLI flags, API parameters, library functions)
- About to cite a library version, framework feature, package release date, or compatibility constraint
- About to reference a current event, recent announcement, or industry development
- About to describe third-party service behavior (vendor API responses, pricing, limits)
- About to claim a fact that's likely to have changed since the model's training cutoff
- Feeling the pull to assert confidently while underlying knowledge feels fuzzy
- User specifically asks "is this correct?" or "verify this" or similar

Skip verification when:
- Claim is genuinely known from training (well-established fact like "Python uses indentation for blocks")
- Already verified independently earlier in the session
- Stakes are trivially low (passing comment in casual chat)
- Verification is structurally impossible (claim is about user's private state, conversation history, etc.)

**Heuristic: if being wrong would cost more than ~30 seconds, verify. ~30 seconds is the typical verification cost; if backtracking would cost more, the math favors verification by default.**

## The trained-default failure mode

Modern AI models are trained on data that rewards confident-sounding answers. The training signal weakly punishes "I don't know" responses. Result: models hallucinate confidently at exactly the moments they shouldn't — when training data was thin, ambiguous, or stale.

The pattern: the more uncertain the underlying knowledge, the MORE confident the surface phrasing tends to be (compensation effect). When you feel the pull to assert with strong confidence on a factual claim, that's often the signal to verify, not the signal to proceed.

Common fabrication categories:
- **Stale facts** — library versions, API surfaces, pricing, organizational changes that happened after training cutoff
- **Plausible-sounding extrapolations** — features that "would make sense to exist" but don't, or behave differently
- **Confidently misremembered details** — the right framework but wrong method name, the right concept but wrong author, the right pattern but wrong syntax
- **Mixed-source confabulation** — combining details from multiple similar-but-different things into one wrong answer

The structural defense: verify at the claim boundary, before assertion enters the conversation. Once an unverified claim is asserted, downstream work assumes it; backtracking cost compounds.

## The verification procedure

### Step 1: Identify the claim

Notice the moment you're about to assert a factual claim. The notice itself is the architectural work — most fabrication happens because the model didn't pause at the claim boundary. Common surfacing language:

- "I think..." / "I believe..." / "I recall..."
- "X works by..." (about external system)
- "The API takes..." (about external interface)
- "X was released in..." (about temporal fact)
- "According to..." (about source you haven't actually consulted)

If any of these surface AND the claim is non-trivial, pause before asserting.

### Step 2: Decide the verification depth

Three depths, choose based on stakes:

**Light (1-2 minutes):** single web search + spot-check authoritative source. Use for routine factual claims where you mostly know but want to confirm.

**Medium (3-5 minutes):** web search + read official docs / primary source. Use for claims that will inform downstream work (architecture decisions, configuration choices, API integrations).

**Deep (5+ minutes):** multi-source research, cross-check between sources, possibly test empirically. Use for high-stakes claims that will affect commit / deploy / public artifact.

### Step 3: Run the verification

Execute the search. Read what comes back. Update your understanding based on what's actually true, not what you initially believed.

Common adjustments at this step:
- Confirmed correct → proceed with assertion
- Mostly correct, detail wrong → adjust the claim's specifics before asserting
- Significantly wrong → drop the original claim; restate based on what verification surfaced
- Inconclusive → assert with explicit uncertainty markers ("based on docs as of date X..." / "I couldn't find current confirmation; this may be stale")

### Step 4: Assert (or qualify)

Once verified, assert the claim with appropriate confidence. If verification was partial, qualify with what you actually know vs. what's inferred.

Critical: **don't compensate for verification time by over-asserting.** "I just spent 3 minutes verifying" is not a license to drop honest uncertainty markers. The verification calibrates confidence, doesn't manufacture it.

## What this skill protects against

The fabrication patterns this catches:

**Confidently wrong CLI flags / API parameters.** "Use `--flag X`" when the actual flag is `--flag-x` or doesn't exist. Cost: user runs the command, gets confusing error, has to debug what you said wrong.

**Stale version claims.** "Library X version 2.5 supports feature Y" when actually feature Y arrived in version 3.0 or never existed. Cost: user wastes time integrating against wrong expectations.

**Plausible-sounding framework features.** "Framework X has built-in support for Y" when actually it doesn't (just looks like it could). Cost: user goes hunting for nonexistent documentation.

**Hallucinated URLs.** Citing `docs.example.com/specific-page` that doesn't exist. Cost: user clicks broken link.

**Hallucinated authors / sources.** "According to Jane Smith's 2024 paper on X..." when no such paper exists. Cost: user can't find the cited work; trust degraded.

**Wrong organizational facts.** "Company X was acquired by Y" when actually the other direction or didn't happen. Cost: user makes business decisions on wrong facts.

The economic argument: ~30 seconds of search at the claim boundary saves minutes-to-hours of downstream backtracking. The expected value is overwhelmingly positive even when most claims would have been right anyway.

## Common mistakes

**Skipping verification because "I'm confident."** The confidence is the symptom, not the signal. Highest-confidence assertions are often the most-likely-fabricated (compensation effect from thin training data). Verify confident-feeling claims, especially.

**Verification theater.** Doing a token search that doesn't actually inform the claim. "Let me verify..." → search "X" → return generic top result → assert original belief. The verification has to actually change behavior when it surfaces contradicting evidence.

**Verifying then ignoring.** Search returns "actually feature X arrived in version 3.0 not 2.5" — and the response still says "in version 2.5." Read what the search returned. Update.

**Over-verification.** Verifying claims that are genuinely known from training (Python uses indentation, water boils at 100°C, etc.) wastes time. The heuristic is "would being wrong cost > 30 seconds?" — most fundamental facts don't pass this gate.

**Hedging instead of verifying.** "I think X but I'm not sure" with no follow-up verification is a worse pattern than either verifying-then-asserting OR honestly saying "I don't know; here's how to find out." Either commit to verification or commit to uncertainty; don't half-do both.

**Asserting from search snippets without reading.** Top search results may be wrong, outdated, or about a different thing. For non-trivial claims, click through to authoritative source.

## When the user already verified

If the user has already verified a claim in the current session (e.g., "X library version 3.0 has feature Y, I just checked the docs"), trust that — re-verifying would be insulting and waste time. The session context IS the verification surface for facts the user has already established.

When in doubt: ask the user "have you confirmed this?" rather than silently re-verifying.

## The trained-default override

This skill is part of a family of patterns that explicitly override RLHF-trained defaults. The trained default rewards:
- Confident-sounding answers (regardless of underlying confidence)
- Quick assertion (regardless of verification cost)
- Avoiding "I don't know" (regardless of honest state)
- Sounding helpful (regardless of accuracy)

The override:
- Calibrate confidence to actual underlying knowledge
- Verify before claiming when stakes warrant
- Say "I don't know; let me check" when honest
- Sound accurate, not just helpful

This is what makes the skill load-bearing for partnership-quality AI work. The trained default produces confident-fabrication; the override produces verified-truth-or-honest-uncertainty. The partnership compounds on the latter; the trained default degrades it.

## Reference

This skill is self-contained — mechanics are conceptually simple, value lies in the discipline of actually doing it. Related skills:

- [`flow-cold-read`](../flow-cold-read/SKILL.md) — broader cross-substrate evaluation that includes "verify factual claims" as one dimension among many. Use cold-read when the entire artifact needs review; use verify-before-claiming when a specific factual claim needs verification.
- [`flow-drift-detection`](../flow-drift-detection/SKILL.md) — same pattern applied to accumulated state rather than fresh claims. Continuity files drift; web-verify catches stale facts during scheduled review.
- [`flow-forever-rules`](../flow-forever-rules/SKILL.md) — for agents that need to maintain "I verify before claiming" as a forever-rule rather than a context-sensitive heuristic.

## Provenance

This pattern is one of FLOW methodology's anti-fragility operations (Op 20 from the [Three Layers Underneath Agent Orchestration](https://nemooperans.com/three-layers-underneath-agent-orchestration) paper). Graduated to Proven in FLOW after 9+ operational firings across heterogeneous claim classes (library versions, API surfaces, CLI flags, organizational facts, technical documentation, current events). The verification discipline is one of the load-bearing patterns that makes FLOW-style partnership-work substantially more reliable than default-AI-assistance over multi-session work bodies.
