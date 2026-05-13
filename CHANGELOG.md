# Changelog

All notable changes to `flow-skills` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### v0.1.0 contents (drafted May 13, 2026 — pre-release)

- **`flow-session-wrap`** — end-of-session memory update following FLOW
  compression discipline: episodic extraction, pattern graduation,
  immune system, structurally-enforced compression. Includes
  `REFERENCE.md` for full mechanics (FlowScript marker spec, continuity
  file structure, graduation grounding rules, active demotion procedure,
  compression guardrails, pre-commit enforcement, bilateral
  cross-substrate integration for multi-agent setups, CLS philosophical
  foundation).
- **`flow-consultation-dispatch`** — multi-AI consultation with
  hidden-assumptions discipline + convergence-divergence interpretation
  + state-machine-tracked artifact. Includes `REFERENCE.md` (full state
  machine spec, review-handoff vs review-process split, architectural
  triangulation, agent selection heuristics by review class,
  consultation file format, anti-patterns deep dive). Cross-validated
  by Tony Sturnus's independent production derivation (May 2026).
- **`flow-cold-read`** — single cross-substrate evaluator dispatched
  against a substantial draft before commit/ship/send. Self-contained
  SKILL.md (no REFERENCE.md — mechanics are conceptually simple).
- **`flow-sourdough-scoping`** — commit-the-shape + adversarial review
  iteration. Self-contained SKILL.md. Proven across 9+ operational
  instances in production (April-May 2026).
- **`flow-flowscript-encoding`** — FLOW Op 9 from Three Layers paper.
  Semantic notation with required-field constraints forcing precision
  while compressing density. State markers, relationship indicators,
  confidence/urgency modifiers. Self-contained SKILL.md. Foundational
  for other skills that use FlowScript encoding internally.
- **`flow-web-verify-before-claiming`** — FLOW Op 20. Anti-fragility
  pattern: verification-before-assertion at claim boundary defeats
  confident-fabrication failure mode. Self-contained SKILL.md.
- **`flow-drift-detection`** — FLOW Op 21. Scheduled adversarial review
  of persistent state catches drift from reality. Companion to
  `flow-cold-read` (fresh drafts) at accumulated-state layer. Cadence
  is the structural defense. Self-contained SKILL.md.
- **`flow-forever-rules`** — FLOW Op 22. Declared invariants encoded at
  identity/foundation layer rather than context layer. Trust-breach
  diagnostic distinguishes forever-class from context-sensitive.
  Structural protection over discipline. Self-contained SKILL.md.
- **`flow-structural-invariants`** — the foundational engineering
  pattern: verification structurally unskippable at the failure point
  beats discipline-based "remember to check X." Six concrete
  sub-patterns (parity tests, CAS, single canonical state machines,
  dead code elimination, explicit phase flags, explicit known-set
  merge). Most-cited Proven primitive in FLOW (11+ operational
  firings). Self-contained SKILL.md.
- **`flow-review-before-publish`** — three orthogonal review types
  layered at publish boundaries: adversarial + session-code-review +
  multi-agent. Each catches a different bug class. Completion pressure
  peaks at publish AND session-end — exactly when review matters most.
  Anti-completion-theater architecture. Self-contained SKILL.md.

### Planned for v0.2.0+ (rolling cadence, one skill per release)

- **`flow-morning-ritual`** — claim-extraction-before-routing methodology
  for daily cognitive intake from autonomous overnight intel.
- **`flow-bilateral-relay`** — cross-AI communication with decision
  artifacts not reasoning chains.
- **`flow-mode-detection`** — adjusting operational profiles based on
  the type of work being performed. From Three Layers paper Op 16.
- **`flow-project-memory-surfaces`** — maintaining separate continuity
  files for distinct work bodies, loaded contextually. From Three
  Layers paper Op 17.
- **`flow-inbox-message-bus`** — inter-subsystem message bus enabling
  autonomous coordination between agents. From Three Layers paper Op 19.

(Operations covered by existing skills: pattern graduation + episodic
extraction subsumed into `flow-session-wrap`. Operations 5/6/11/14/15/18
deliberately not standalone-skilled — substrate-implementation-specific
or meta-pattern-too-abstract for portable SKILL.md form.)

### Three Layers paper coverage status (10 of ~17 standalone-skillable ops)

Ops directly covered by shipped skills: 1, 2, 3, 4, 7, 8 (session-wrap), 9
(flowscript-encoding), 12 (in flowscript-encoding), 13 (consultation +
cold-read), 20 (web-verify), 21 (drift-detection), 22 (forever-rules).

Plus methodology-layer skills not mapping to specific ops:
sourdough-scoping (sibling to structural-invariants at methodology
layer), structural-invariants (foundational engineering pattern,
ancestor of multiple ops), review-before-publish, cold-read (anti-RLHF
defense at draft boundary).

Ops scoped as future-skill: 16, 17, 19. Ops scoped as not-skill-class:
5, 6, 11, 14, 15, 18.

[Unreleased]: https://github.com/phillipclapham/flow-skills/compare/v0.1.0...HEAD
