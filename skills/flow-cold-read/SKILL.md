---
name: flow-cold-read
description: When you've drafted something substantial in one AI substrate (an analysis, synthesis, decision rationale, code review, architectural plan, essay, or any artifact about to be committed/shipped/sent), dispatch a single cross-substrate evaluator — a different model family from the one that drafted — to read it cold and surface what same-substrate review cannot see. Different training distributions catch different blind spots: motivated reasoning, over-claiming, retrieval-mistaken-for-synthesis, template-overreach, observability-suppression-by-prompt patterns invisible from inside the substrate that produced the draft. Use as a phase-gate before high-stakes commits, not as per-line polish. Multi-instance convergent flags from cold-read signal structural pivot — not chapter-by-chapter editing. $0 marginal cost when both substrates are accessible. Trigger phrases: "cold read this", "second pair of eyes", "what am I not seeing", "review before I ship".
---

# flow-cold-read

Cross-substrate evaluation methodology. Dispatch a different model family to read your draft cold — same-substrate analysis cannot see motivated reasoning + over-claiming + template-overreach patterns that cross-substrate review catches trivially. Phase-gate at session-class structural review boundaries, not per-line polish. $0 marginal cost when both substrates are accessible via subscription.

## When to use

Trigger conditions:
- User says "cold read this", "second pair of eyes", "what am I not seeing", "review before I ship", "before I commit", "before I send"
- You've drafted something substantial in one substrate and you're about to commit / ship / send / publish
- You've spent enough time inside the draft that you can't reliably see it any more
- Architectural decision recorded; about to act on it
- Pre-publish quality gate for any external-audience artifact
- The work is hard to reverse — undoing requires expensive backtracking

Do NOT use for:
- Per-line polish or chapter-by-chapter editing (use a content editor / domain reviewer instead — cold-read is structural, not stylistic)
- Decisions that are easy to reverse (trial-and-error is cheaper than cold-read overhead)
- Drafts you haven't yet completed (cold-read is a phase-gate, not a brainstorm partner)
- Quick decisions where the substrate that drafted is also genuinely the best to review (rare; but possible for highly specialized domains)

## What this skill assumes

This skill assumes the agent has access to at least 2 different AI substrates (different model families or different training distributions). The substrate that DRAFTED is different from the substrate that COLD-READS.

Common setups:
- Drafted in Claude → cold-read by Codex (or Gemini)
- Drafted in Codex → cold-read by Claude
- Drafted in Gemini → cold-read by Claude

Same model family at different temperatures is NOT cross-substrate evaluation. Same training distribution → same blind spots → no orthogonal value.

If only one substrate is available, this skill cannot run. Use [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) with the same substrate at different roles as a partial substitute, but it's strictly weaker than cross-substrate cold-read.

## The cold-read procedure

### Step 1: Identify the artifact + the question

Cold-read is most valuable when the evaluator has a specific question, not just "review this generally."

Effective questions:
- "Does this analysis hold under scrutiny? Where am I motivated-reasoning?"
- "Does this synthesis actually synthesize, or is it retrieval-mistaken-for-synthesis?"
- "Does this architectural decision have hidden assumptions I'm not seeing?"
- "Is this draft over-claiming relative to the evidence I cite?"
- "Is this code review missing a class of bugs?"

Less effective questions:
- "What do you think?" (too open — produces generic best-practices noise)
- "Is this good?" (validation theater — produces yes/no anchored to politeness)

### Step 2: Frame the cold-read prompt

Provide:

1. **The artifact in full.** Don't summarize — let the evaluator read what was drafted.
2. **Brief context.** What is this for? What's the audience? What's at stake?
3. **The specific question.** Not "review" — the actual concern that's worth their cross-substrate read.
4. **What you've already checked.** The evaluator should focus on what you haven't seen, not duplicate what you've already self-reviewed.
5. **Permission to be direct.** Default AI politeness compresses divergent findings into hedged validation. Explicitly: "be direct; if this is over-claiming, say so plainly. If this is fine, say so plainly. I'd rather hear hard truth than soft validation."

### Step 3: Dispatch + read

Send the prompt to the cross-substrate evaluator. Read the response cold yourself — don't let the original-substrate's framing of "what I meant" override what the cold-reader actually saw.

Cold-readers commonly surface:
- **Motivated reasoning** — claims you wanted to be true and constructed evidence around, rather than claims that emerged from evidence
- **Over-claiming** — confidence level exceeds the evidence's support
- **Retrieval-mistaken-for-synthesis** — apparent insight is actually paraphrasing of source material with cosmetic recombination
- **Template-overreach** — pattern you've been using elsewhere getting applied where it doesn't fit
- **Observability-suppression-by-prompt** — instructions that pre-empted scrutiny (e.g., "be confident", "don't hedge") produced inappropriate confidence
- **Hidden assumptions** that you can't see because they're in your substrate's load-bearing priors

### Step 4: Decide what to do with findings

Three classes of cold-read findings:

1. **Substantive findings you accept.** Update the artifact before shipping. Cold-read paid for itself.
2. **Substantive findings you reject with rationale.** Sometimes the cold-reader is wrong (different priors, missing your context, training-distribution-specific blind spot of theirs). Record why you reject — that's an audit trail.
3. **Surface-level polish suggestions.** Often present in cold-read output (cross-substrate readers naturally do some grammar/style commentary too). Optional — adopt if useful, skip if not. These are NOT what cold-read is for.

The 1st class is the value. If cold-read consistently surfaces nothing substantive, either you're already excellent at self-review (rare) or you're using cold-read for the wrong artifact class (the draft isn't substantial enough to warrant it).

### Step 5: Re-read your draft with cold-reader's eyes

Even after addressing specific findings, do one final pass over the artifact with the cold-reader's framing in mind. Often the explicit findings surface a *category* of issue that has 3-5 instances throughout the artifact — only one of which the cold-reader specifically called out.

## Phase-gate, not per-line polish

Cold-read is a **phase-gate** for session-class structural reviews. Not a per-line editing pass.

**Use at:**
- Pre-publish (before LinkedIn / blog / paper goes out)
- Pre-commit (before architecture decision becomes the substrate)
- Pre-ship (before code reaches users)
- Pre-send (before email/DM reaches recipient)
- Post-major-revision (after restructuring an artifact, before declaring done)

**Don't use for:**
- Chapter-by-chapter editing (use a content editor — cold-read isn't structural enough for line-level work AND not domain-specific enough for craft-level concerns)
- Iterative drafting (use the same substrate for iteration; cold-read once when you think you're done)
- Decisions you haven't formed yet (cold-read of nothing produces noise about nothing)

## Multi-instance convergent flags signal structural pivot

If you cold-read the same artifact at 3+ revision boundaries and the cold-reader keeps surfacing convergent flags — even though you've addressed each instance — that's signal that the underlying STRUCTURE has the problem, not the chapters.

Pattern recognition:
- Cold-read at v1 of artifact: flags "competence-loop with deferred dramatic cost"
- Cold-read at v3 (after addressing v1 + v2 flags): flags "fatigue-point momentum-stall"
- Cold-read at v5 (after addressing v3 + v4 flags): flags "cross-chapter-scope hedges"

If each individual flag seems like a per-chapter editing target but the convergence across revisions points to the SAME structural issue (e.g., "the artifact's framing doesn't support its weight"), the right response is **structural pivot**, not another revision pass.

This pattern surfaced in production novella revision (May 2026) — Codex/Gemini cold-read substrate at Ch 3 + Ch 5 + Ch 6 had been flagging different-named issues across versions; all were signal that the literary frame itself was wrong and a commercial-genre pivot was the structural answer. Per-chapter polish would not have resolved it.

**Rule of thumb:** if 3+ cold-reads at the same artifact-class produce convergent flags despite per-chapter response, stop and re-read the FRAME. The substrate-pivot may be the answer.

## Common mistakes

**Same-substrate cold-read.** Same model family at different temperatures = same blind spots. No orthogonal value. The architectural premise is different training distributions.

**Cold-read of a draft you haven't completed.** Cold-read of incomplete work surfaces "this isn't done yet" findings — useful as a self-review prompt but not what the phase-gate is for. Complete the draft first.

**Treating cold-read as validation.** Default AI politeness compresses divergent findings into soft validation. If your cold-read prompt didn't explicitly permit directness, you're getting validation theater. Re-prompt asking for hard reads.

**Adopting all findings uncritically.** Sometimes cold-readers are wrong. Reject with rationale; don't capitulate. The cold-reader has cross-substrate blind spots too.

**Single cold-read on an iterative draft.** If you cold-read once early, address the findings, then ship without re-reading, you've used cold-read as a brainstorm partner — useful but not what the phase-gate is for. Cold-read is the LAST step before commit.

**Confusing cold-read with full consultation.** Cold-read is single-evaluator, single-substrate. Consultation is parallel multi-agent with synthesis. Different patterns for different needs. Cold-read is faster, lighter, cheaper. Consultation is heavier and surfaces more dimensions. Use cold-read when the question is "am I seeing this right?" — use consultation when the question is "what's the best answer here?"

## Cost economics

At AI subscription pricing in 2026, cold-read marginal cost is effectively $0:

- If you're on Claude Code (Pro/Max), Claude API calls are bundled
- If you're on Codex CLI (ChatGPT Pro), Codex calls are bundled
- Subscription costs sunk regardless

So cold-read pays out in *unrolled-back work*. One avoided over-claim in a published essay is worth dozens of cold-reads. One caught architectural blind spot saves hours-to-days of backtracking.

The dominant economics: cold-read at every session-class structural review boundary. The expected value per cold-read is positive even when most surface nothing substantive — because the few that do save days.

## Reference

This skill is self-contained — its mechanics are simpler than [`flow-consultation-dispatch`](../flow-consultation-dispatch/SKILL.md) (which uses cold-read principles but at higher orchestration cost with parallel multi-agent + synthesis). If you need the deeper version, dispatch a consultation instead. If you need the lighter version, this is it.

Related: [`flow-session-wrap`](../flow-session-wrap/SKILL.md) (the end-of-session consolidation skill) — cold-read can be applied to a wrap's continuity output before commit as a structural check on the wrap quality.

Provenance: cross-substrate evaluation methodology graduated to "Proven" in FLOW methodology after 4 operational instances during sovereignty-spike work in Apr-May 2026 (Sessions 5, 6.2, 6.3, 6.4 of the spike pulled cross-substrate cold-reads from Codex GPT-5.3 substrate). Methodology operationalized as the per-session phase-gate at session-class structural reviews; $0 marginal cost confirmed across the full graduation window.
