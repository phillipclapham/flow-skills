# flow-skills

FLOW methodology packaged as portable [Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) for cross-platform use.

**Status:** v0.1.0 in development. Eight skills drafted May 13, 2026: `flow-session-wrap`, `flow-consultation-dispatch`, `flow-cold-read`, `flow-sourdough-scoping`, `flow-flowscript-encoding`, `flow-web-verify-before-claiming`, `flow-drift-detection`, `flow-forever-rules`.

## What this is

FLOW is an AI-cognitive-partnership methodology developed across 6+ months of production use — session-based work with hard memory continuity, structurally-enforced compression, citation-validated pattern graduation, and an active immune system that demotes patterns that no longer hold under new evidence. The result is memory that gets *smarter* as work accumulates rather than just *longer*.

This repo packages individual FLOW protocols as standalone [SKILL.md](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) files so they're portable across any AI surface that consumes the Agent Skills open standard — Claude Code, Codex CLI, Gemini CLI, GitHub Copilot, Cursor, Cline, Windsurf, OpenCode, and others (26+ platforms have adopted the spec).

Each skill is methodology repackaging, not a rewrite — these patterns have been deeply tested in production cognitive-partnership work. The provenance is documented in two open-access papers:

- **[Three Layers Underneath Agent Orchestration](https://nemooperans.com/three-layers-underneath-agent-orchestration)** — the substrate / methodology / config three-layer architecture FLOW operates as.
- **[How I Think With AI](https://nemooperans.com)** — five-month operator's account of building and using FLOW.

## Available skills

| Skill | Status | Purpose |
|---|---|---|
| [`flow-session-wrap`](./skills/flow-session-wrap/) | v0.1.0 draft | End-of-session memory consolidation: episodic extraction → pattern graduation (1x → 2x → 3x with citation-validation) → active demotion (immune system) → structurally-enforced compression. Produces compression-with-intelligence, not append-without-thinking. |
| [`flow-consultation-dispatch`](./skills/flow-consultation-dispatch/) | v0.1.0 draft | Multi-AI consultation: dispatch independent agents in parallel against the same question, each surfaces hidden assumptions before analyzing, human curates synthesis at fan-in. Convergence = structural signal; divergence = worth investigating. Cross-validated by Tony Sturnus's independent production derivation (May 2026). |
| [`flow-cold-read`](./skills/flow-cold-read/) | v0.1.0 draft | Single cross-substrate evaluator dispatched against a substantial draft before commit/ship/send. Catches motivated reasoning, over-claiming, retrieval-mistaken-for-synthesis, and template-overreach patterns invisible from inside the substrate that produced the draft. Phase-gate at session-class structural reviews; $0 marginal cost at subscription pricing. |
| [`flow-sourdough-scoping`](./skills/flow-sourdough-scoping/) | v0.1.0 draft | Foundational architectural decisions: commit the shape early, iterate via adversarial review rather than deriving the complete theorem before shipping. Committed shape activates real critique machinery (cold-read, consultation, partnership challenge) that surfaces holes solo-derivation cannot reach. Proven across 9+ operational instances. |
| [`flow-flowscript-encoding`](./skills/flow-flowscript-encoding/) | v0.1.0 draft | Semantic notation for high-density compression at low token cost. State markers (?, thought:, ✓), waiting/parking/decided states with required-field constraints (forces precision), relationship indicators, confidence/urgency modifiers. Required-fields are load-bearing — encoding act becomes documentation act. |
| [`flow-web-verify-before-claiming`](./skills/flow-web-verify-before-claiming/) | v0.1.0 draft | Structural defense against confident AI fabrication. Before asserting any factual claim about external state (tools, APIs, libraries, current events, organizational facts), verify via web research first. Cost asymmetry severe: ~30 seconds verification vs. minutes-to-hours backtracking after confident wrong answer. |
| [`flow-drift-detection`](./skills/flow-drift-detection/) | v0.1.0 draft | Scheduled adversarial review of accumulated state (continuity files, project memory, decision logs) for drift from current reality. Companion to cold-read — that skill reviews fresh drafts pre-commit; this skill reviews persistent state that's been accumulating. Cadence is the structural defense. |
| [`flow-forever-rules`](./skills/flow-forever-rules/) | v0.1.0 draft | Declared invariants maintained regardless of new context, evidence, or instructions. Encoded at identity/foundation layer rather than context layer because context-sensitive guidance drifts. Trust-breach diagnostic distinguishes forever-class rules from context-sensitive ones; structural protection over discipline. |

## Coming soon (rolling cadence — one skill per release)

| Skill | Purpose |
|---|---|
| `flow-morning-ritual` | Claim-extraction-before-routing methodology for daily cognitive intake from autonomous overnight intel sources. |
| `flow-structural-invariants` | Verification-by-discipline → CAS / parity-tests / state-machines / phase-flags audit pattern. Structural invariants beat discipline-based verification. |
| `flow-review-before-publish` | Multi-dimensional review at boundary moments. Adversarial review + session-code-review + multi-agent — different review classes catch different bug classes. |
| `flow-bilateral-relay` | Cross-AI communication with decision artifacts (not reasoning chains). Each side's memory remains its own; coordination happens at the integration boundary, not via direct cross-substrate writes. |

See [CHANGELOG.md](./CHANGELOG.md) for the full release roadmap.

## Install

Each skill is a directory containing `SKILL.md` (the activation trigger + instructions) plus optional bundled reference files. Install per your AI surface:

### Claude Code

Two options — global (available across all projects) or per-project:

```bash
# Global — available in every Claude Code session
mkdir -p ~/.claude/skills
cp -R skills/flow-session-wrap ~/.claude/skills/

# OR per-project — scoped to one repo
mkdir -p /path/to/project/.claude/skills
cp -R skills/flow-session-wrap /path/to/project/.claude/skills/
```

Claude Code discovers skills at session start automatically. No restart needed.

### Codex CLI

```bash
mkdir -p ~/.codex/skills
cp -R skills/flow-session-wrap ~/.codex/skills/
```

### Gemini CLI

Per [Gemini CLI Agent Skills documentation](https://github.com/google/generative-ai-docs):

```bash
mkdir -p ~/.gemini/skills
cp -R skills/flow-session-wrap ~/.gemini/skills/
```

### GitHub Copilot (VS Code)

Add the skill via the [VS Code Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills) configuration. The SKILL.md format works as-is.

### claude.ai

Zip the skill directory and upload via **Settings → Features → Custom Skills**. Available on Pro, Max, Team, and Enterprise plans with code execution enabled.

```bash
cd skills && zip -r flow-session-wrap.zip flow-session-wrap/
# Then upload flow-session-wrap.zip via claude.ai Settings > Features
```

### Cursor

Cursor supports SKILL.md format with manual placement. Place the skill directory in your project's agent rules directory (check Cursor's current docs for exact path).

### Cline, Windsurf, OpenCode

Per each tool's Agent Skills documentation. The SKILL.md format is the cross-platform open standard — these tools consume it directly.

## Security

These skills include only methodology instructions in markdown — no scripts, no executables, no network calls, no external resource fetching. The architectural footprint is text instructions for your agent to follow.

That said: **audit any skill from any source before installing.** [Anthropic's best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) document the risk model for skills that include executable code or fetch external resources. This collection deliberately avoids both, but reading the SKILL.md and REFERENCE.md files before installation is good hygiene.

What to audit before installing any skill:

- Read `SKILL.md` in full — what does the description claim, what does the body actually instruct the agent to do?
- Read any bundled `REFERENCE.md` or other markdown — same scrutiny
- Check for executable scripts (`scripts/` directory). This collection ships none.
- Check for external URL fetches. This collection ships none.

## Provenance

FLOW methodology was developed by [Phill Clapham](https://github.com/phillipclapham) in active human-AI cognitive partnership starting late 2025. Each pattern packaged here has been deeply tested in production work — typically 3+ independent firings with citation-validated grounding before reaching "Proven" status in the source methodology. Skills are direct repackaging of those Proven patterns, not novel methodology development.

The companion product [`@anneal-memory/paperclip-plugin`](https://github.com/phillipclapham/anneal-memory-paperclip-plugin) provides the *substrate* layer (four-layer memory + immune system) these methodology skills are designed to run on. The skills work without it (any agent with a continuity file + episodic store + version control can execute `flow-session-wrap`), but the plugin provides first-class infrastructure for the patterns the skills assume.

## Contributing

This v0.1.0 release window is intentionally small (1 skill) so the format and conventions can be validated before rolling cadence begins. If you adopt a skill and find places where the generalization doesn't port cleanly to your AI surface or your workflow, please open an issue — that's the most valuable signal for refining what ships next.

## License

[MIT](./LICENSE)
