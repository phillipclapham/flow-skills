---
name: flow-consultation-dispatch
description: When facing a non-trivial decision, code review, architectural choice, or any artifact about to be committed/shipped/sent, dispatch multiple independent AI agents in parallel for cross-substrate evaluation, then synthesize at the fan-in step. Each agent surfaces hidden assumptions before analyzing — that forced enumeration is where cross-substrate value lives. Convergence across different training distributions is structural signal; divergence is what's worth investigating. Use when the user says "review this", "get a second opinion", "consult on this", "sanity check", "what does the team think", or before any decision/commit that's hard to reverse. The human (or orchestrator) curates synthesis at fan-in — automating that step collapses the value back to a single-substrate echo chamber.
---

# flow-consultation-dispatch

Multi-AI consultation pattern. Dispatch independent agents (different training distributions) in parallel against the same question. Each agent surfaces its hidden assumptions before analyzing — that forced enumeration is where cross-substrate value lives. Synthesis happens at the fan-in step; that step is **load-bearing** and should not be automated. Convergence is structural signal. Divergence is the most valuable signal.

## When to use

Trigger conditions:
- User says "review this", "get a second opinion", "consult on this", "sanity check", "what does the team think", "review these changes"
- Facing a non-trivial decision with significant downside risk if wrong
- About to commit / ship / send / publish an artifact hard to undo
- Architecture / design decision at a foundational boundary
- Stuck on a problem after 2+ failed attempts (different training distribution may catch what the current substrate is conflating)
- Pre-publish quality gate for an external-audience artifact

Do NOT use for:
- Trivial decisions where cost of being wrong is low
- Decisions already validated by recent independent evidence
- When you genuinely already know the answer and are seeking validation theater (the consultation will surface that — better to skip it)

## What this skill assumes

This skill assumes the agent has access to at least 2 different AI substrates (different model families or different training distributions). Common setups:

- Multiple CLI tools: Claude Code + Codex CLI + Gemini CLI (different model substrates, different prompts)
- API access to multiple model families (Claude API + OpenAI API + Gemini API)
- A multi-agent orchestration tool (LangGraph, AutoGen, custom orchestrator)
- Manual coordination: copy-paste the question into different AI surfaces and gather responses

Single-substrate echo chamber (one model called multiple times, even at different temperatures) is NOT cross-substrate evaluation. The architectural premise is different training distributions → different blind spots → convergence is informative.

## The consultation procedure

### Step 1: Frame the question precisely

A consultation is only as good as its prompt. Before dispatching, the prompt should include:

1. **The specific question or decision.** Not "what do you think about X" — "should we use Postgres or Redis for sessions given constraints A, B, C, and our risk tolerance is D?"
2. **Relevant context.** Include the code/artifact being reviewed, prior decisions that constrain the space, and what's already been tried/ruled out.
3. **Decision authority.** Who's making the final call (you / the user / a downstream reviewer). This shapes the framing of recommendations vs. analysis.
4. **Hidden Assumptions section requirement.** Critical: instruct each agent to surface its own hidden assumptions BEFORE analyzing. "Before your analysis, list 3-5 assumptions you're making that, if wrong, would change your answer." This forced enumeration is where cross-substrate value concentrates.

### Step 2: Dispatch in parallel

All agents work in parallel, each in isolation. Critical: **agents do NOT see each other's responses while drafting their analysis.** Independence is what makes convergence informative. If agent B reads agent A's output before drafting, B anchors to A and you've collapsed to a single-substrate analysis with extra steps.

Each agent receives the same framed prompt. Each writes its analysis to its own section of a shared artifact (or returns to the orchestrator individually).

### Step 3: Collect — state machine in motion

The consultation moves through explicit states:

- **collecting** — at least one agent has responded; waiting for others
- **ready_for_synthesis** — all dispatched agents have written; nothing left to wait for
- **synthesized** — the human/orchestrator has produced the synthesis output
- **decided** — synthesis surfaced a decision; decision is recorded
- **closed** — consultation file marked complete; downstream work proceeds

Make the state explicit in the consultation artifact. "Status: ready_for_synthesis" at the top of the file means the orchestrator can move to Step 4. "Status: collecting" means another agent is still working. Don't pre-synthesize on partial input.

### Step 4: Synthesize at fan-in (LOAD-BEARING — do not automate)

The fan-in step is where the consultation's value concentrates. The synthesizer (human, or in a structured orchestration, a designated synthesis role) reads all agent responses and produces:

1. **Convergence** — where do the agents agree? Convergence across different training distributions is **structural signal**. If three models from different substrates all say X, X is probably true at the architectural layer, not just an artifact of one model's training.
2. **Divergence** — where do they disagree? Divergence is the most valuable signal in the consultation. It marks something worth investigating — either a real tradeoff (where reasonable models can split based on priors), or a place where one model has insight others lack.
3. **Unique contributions** — what did each agent surface that the others didn't? Often the most insightful finding came from exactly one agent — preserve it; don't average it away.
4. **Hidden Assumptions audit** — what assumptions are the agents making in common? What does that tell you about your prompt's frame?
5. **Recommended actions** — synthesized direction. Not majority vote. Curated.

**Why not automate this step:** automating the synthesis collapses the consultation back to a single-substrate echo chamber with extra steps. The synthesizer's judgment about *which divergence is investigable signal vs. taste-level disagreement* is the architectural value. A naive automated synthesizer averages, which destroys the divergence that was the whole point.

### Step 5: Decide + record

Based on the synthesis, the decision-maker (often the user) either:

- Decides — records the decision with rationale, transitions state to `decided` → `closed`
- Defers — needs more information, returns to Step 1 with a sharpened question
- Splits — surfaces sub-questions, may spawn child consultations

The consultation artifact stays as audit trail. When a similar question comes up in 6 weeks, prior consultations are searchable evidence.

## Hidden Assumptions discipline (per-agent)

**This is the load-bearing section.** Without it, agents default to "best practices" / "industry standard" / their training-time priors — useful but not what consultation is for. With it, each agent surfaces what's *invisible from inside its own substrate*.

Each agent's response should start with:

```
## Hidden Assumptions

I'm assuming:
1. [Assumption A] — if wrong, my answer would change because [explanation]
2. [Assumption B] — ...
3. [Assumption C] — ...
```

When the synthesis step compares Hidden Assumptions sections across agents, you'll often find:

- Agents make *different* assumptions invisible to themselves. Agent A assumes the team has 10 engineers; agent B assumes 3. That difference probably explains divergence in their recommendations.
- Agents make the *same* assumption invisibly. If all three assume the same thing, the synthesizer should ask: is that assumption load-bearing for the question? Should the prompt have stated it explicitly?

The Hidden Assumptions section is not optional. It's the discipline that makes the consultation produce orthogonal value rather than convergent generic best-practices noise.

## Convergence vs divergence — how to read the signal

| Pattern | Meaning | Action |
|---|---|---|
| All agents converge on X | Structural signal — X is probably true at the architectural layer | Trust the convergence; proceed |
| All agents diverge wildly | Prompt was probably unclear or the question is genuinely open | Sharpen the prompt; re-dispatch |
| 2 of 3 converge, 1 dissents with strong rationale | The dissenter often has the most valuable finding | Investigate the dissenting view |
| 2 of 3 converge, 1 dissents with weak rationale | Likely a substrate-specific quirk in the dissenter | Acknowledge, proceed with convergence |
| Each agent unique, all coherent | Different valid angles on a real tradeoff | Synthesizer's call — what priors apply here? |
| All "yes but" with different "buts" | Each substrate sees a different blind spot — high-value consultation | Take all the buts seriously; redesign |

**General principle:** convergence is structural signal, divergence is what's worth investigating. Don't average; curate.

## Common mistakes

**Letting agents see each other's responses before drafting.** Collapses independence; you've lost the consultation's value. Each agent must draft in isolation. If you're manually coordinating, copy the prompt into all surfaces before reading any responses.

**Automating the synthesis.** The fan-in step IS the value layer. Automating collapses it back to single-substrate echo chamber with extra steps.

**Skipping Hidden Assumptions.** Default agent responses surface "best practices" priors — useful but not what consultation is for. Forced enumeration of hidden assumptions is what makes the cross-substrate diversity informative.

**Majority voting on divergence.** "2 of 3 said X, so X wins." This destroys the divergence that was the whole point of consultation. The dissenter often had the most valuable finding. Investigate divergence, don't vote it away.

**Pre-synthesizing on partial input.** When only 1 of 3 agents has responded, don't start drafting synthesis. Wait for `ready_for_synthesis` state. Pre-synthesis anchors the synthesizer's reading of the later responses.

**Treating consultation as validation.** If you already know the answer and are dispatching to feel better about it, the consultation will either confirm what you wanted (no new information) or surface a divergence you'll rationalize away (no integrity). Skip the consultation; just commit.

## Reference

See [REFERENCE.md](./REFERENCE.md) for: complete state machine specification, review-handoff vs review-process split (one-pass closure with strict preflight + one-shot synthesis), architectural triangulation (later reviewers read prior reviews to target blind spots, not duplicate them), agent selection heuristics by review class (code / architecture / docs / strategy), full Hidden Assumptions section template, review intent collision rules (when findings conflict with prior decisions), and consultation file format examples. Provenance notes on Tony Sturnus's independently-derived implementation that cross-validated this architecture in production lead-gen agent work.
