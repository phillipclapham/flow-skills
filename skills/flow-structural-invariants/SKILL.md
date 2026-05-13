---
name: flow-structural-invariants
description: When writing code, designing systems, or specifying processes that require verification (a check passes, a precondition holds, an invariant is preserved), do not rely on discipline ("remember to check X") when you can make verification structurally unskippable at the failure point. Verification in a different layer than the mechanism it verifies can be silently defeated when load or execution order bypasses it. The fix is never "verify harder" — it is always "make verification structurally unskippable at the failure point." Concrete patterns: parity tests for equivalence-class bugs, compare-and-swap (CAS) for verify-then-act sequences, single canonical state machine vs. tolerated partial states, dead code elimination over defensive documentation, explicit phase flags over control-flow cleanup ordering, explicit known-set merge over absence-as-signal. Use when designing verification mechanisms, fixing bugs whose root cause is bypassable-discipline, or reviewing code where the failure mode is "we forgot to check." Most-cited Proven primitive in FLOW methodology (11+ operational firings across code, audit chain, scheduler coordination, and content-validation substrates).
---

# flow-structural-invariants

The principle: **discipline → drift → bypass. Invariant → refusal → integrity.** When verification can be moved from discipline-based ("remember to check X") to structural ("X is structurally impossible to skip"), do so. This is the foundational pattern for building systems that don't degrade under load.

The most common engineering failure mode is verification mechanisms in a *different layer* than the mechanism they verify, where load or execution order bypasses the verification step. The fix is never "verify harder" — it is always to relocate verification to the failure point itself.

## When to use

Trigger conditions:
- Designing verification mechanisms (preconditions, postconditions, invariants, audit checks)
- Fixing bugs whose root cause is "we forgot to check X" or "the check passed at the wrong time"
- Reviewing code where the failure mode is bypassable-discipline
- Specifying processes where reliable execution matters across many sessions / many operators / many states
- Building anti-fragile systems (the whole architectural class)
- About to write "REMEMBER:" in a comment as a verification mechanism (that's the smell)

Do NOT use for:
- One-off scripts where reliability doesn't compound
- Genuinely-different-priority concerns (sometimes optimization or readability beats structural rigor for the use case)
- Prototypes designed to be thrown away
- Verification that can't be made structural (rare, but real — some things only humans can check)

## The core principle: structural beats discipline

Discipline-based verification:
- Depends on the operator remembering to do the check
- Drifts under load (when busy / tired / distracted)
- Decays over time (next quarter's operator doesn't know about the rule)
- Bypasses silently (the check still exists; it just doesn't run when it matters)

Structural verification:
- Depends on the system's architecture making the check unskippable
- Holds under load (architecture doesn't care if you're busy)
- Persists over time (the architecture is the documentation)
- Cannot bypass silently (failure to verify is failure to act)

**The diagnostic question:** if a future operator/agent/session doesn't read the comment / doesn't remember the rule / doesn't have the right priors, does the verification still happen? If yes → structural. If no → discipline (and therefore brittle).

## Pattern 1: Parity tests for equivalence-class bugs

When two surfaces should produce the same result (canonical vs derived, code path A vs code path B, original vs migrated), write the equivalence test that mechanically verifies they match.

Discipline version: "Remember to keep these in sync when changing one."
Structural version: Test that fails if they diverge. Run on every CI cycle.

Once the parity test exists, you don't need to remember anything. Either the test passes (they're in sync) or fails (they're not). Discipline → structure.

Example contexts: cross-transport API parity, multi-format export consistency, before/after migration data integrity, canonical-vs-cached comparison.

## Pattern 2: Compare-and-swap (CAS) for verify-then-act

When a verify step is followed by an act step, and the verification is taken at one moment and acted on at another, race conditions and stale-verification bugs become possible.

Discipline version: "Make sure no one modifies the state between verify and act."
Structural version: Move verification INTO the act's atomic boundary as a compare-and-swap. The act fails atomically if the state has changed since verification.

Examples: optimistic concurrency control (verify revision number → act-or-fail-if-changed), conditional writes in databases, atomic file replacement (write to temp + rename — fails atomically if rename target moved).

## Pattern 3: Single canonical state machine over tolerated partial states

When a workflow can exist in N states with M valid-but-different transitions, every "partial" state is a silent-bypass surface where rules can be violated.

Discipline version: "When state is X, do A. When state is Y, do B. When state is X-and-Y-partial, do... it depends."
Structural version: Define exactly the canonical states. Any other state is invalid. Code that produces invalid state fails loudly; code that consumes invalid state fails loudly.

Examples: transaction states (pending / committed / aborted; no "partial commit"), session states (active / wrapped / archived; no "half-wrapped"), build states (success / failure / in-progress; no "succeeded-but-untested").

The architectural insight: tolerated partial states create surface area for rule violation. Canonical states eliminate the surface.

## Pattern 4: Dead code elimination over defensive documentation

When a function / method / helper can ONLY be misused (every legitimate use case should go through a different path), delete the helper. Documenting "don't use this" is discipline. Removing it is structure.

Discipline version: A comment saying "DEPRECATED: use newMethod() instead."
Structural version: The old method is deleted. Calls don't compile.

Examples: removing implicit type conversions that always cause bugs, eliminating "soft delete" methods that always need extra guards, deleting compatibility shims after migration completes.

The compiler / type system becomes the verification mechanism. Future operators can't bypass it because the surface doesn't exist.

## Pattern 5: Explicit phase flags over control-flow cleanup ordering

When cleanup semantics depend on WHICH PHASE raised an error (e.g., pre-commit cleanup deletes; post-commit cleanup preserves), tracking "what phase we're in" via control flow is discipline-based. The phase changes during refactoring; cleanup behavior breaks.

Discipline version: "After this line, we're post-commit, so the except handler should preserve state."
Structural version: An explicit `db_committed = True` flag flipped as the FIRST statement after phase transition. Cleanup handlers branch on the flag. Flag is visible at the cleanup site; survives refactoring.

Example: two-phase commit with rollback (pre-commit error → rollback; post-commit error → log + preserve). Without the flag, the rollback logic relies on remembering which side of the commit you were on. With the flag, the logic is mechanical.

## Pattern 6: Explicit known-set merge over absence-as-signal

When a reconciliation step uses "absent from memory" as a proxy for "added externally," intentional removals get silently undone. The mechanism cannot distinguish "intentionally removed" from "never existed."

Discipline version: "Remember to update both sides when removing items."
Structural version: Pass the known set (loaded IDs) explicitly. Merge logic uses presence-in-known-set as the discriminator, not absence-from-memory. Items absent from known-set but present in store are intentionally removed (don't re-add); items absent from store but present in known-set are externally added (do reconcile).

This catches silent-undo bugs that pure-absence-based merge misses.

## Pattern 7: Verification at the failure point, not the success path

The most subtle invariant pattern: verification belongs at the point where failure to verify is failure to act, not somewhere upstream where verification can be true at the moment of check but false at the moment of action.

Discipline version: Verify at function entry; assume verification holds for the duration of the function.
Structural version: Verify atomically with the action that depends on verification.

Examples:
- Authentication: not "check user.is_authenticated at request start" but "the database query includes the auth predicate"
- Permission: not "check permission before action" but "the action is structurally impossible without the permission" (capability-based security)
- Resource locking: not "check lock state then act" but "acquire lock; act inside lock; release lock" (with timeout)

## Common mistakes

**"Just add a comment" as verification.** Comments aren't verification; they're documentation. Future operators may not read them. Verification has to be in the code path.

**"Add a runtime check" without considering bypass surface.** A runtime check that runs on success path but not error path, on production path but not test path, on synchronous path but not async path — has bypass surface. Verify at the failure point, not somewhere upstream.

**Tolerated "edge cases" instead of eliminated invalid states.** "We allow X but expect it not to happen" creates surface for X to happen. Either X is valid (allow + handle correctly) or X is invalid (reject + fail loudly). The middle creates silent bugs.

**Defensive documentation over deletion.** "DO NOT USE — use newMethod instead" leaves the surface for misuse. Delete the old method. Compiler errors are stronger than comments.

**Discipline-only invariant maintenance.** "All future commits should preserve this property" — that's discipline. Add the test/hook/structural-constraint that fails if a future commit breaks it. The structure persists; the discipline doesn't.

**Verification in the wrong layer.** Checking authentication in middleware that can be bypassed by direct service calls; checking authorization in the controller when the database can be queried directly; checking input validation at the UI when the API accepts unvalidated requests. Find the lowest layer where verification can be unskippable.

**Forgetting that "structural" includes the test suite.** A test that fails when X is violated IS a structural verification — provided the test runs on every commit (CI), the suite is comprehensive (relevant code paths exercise the invariant), and broken tests block merge (not just warn).

## The architectural insight

This pattern is the foundational layer of multiple other FLOW skills:

- [`flow-session-wrap`](../flow-session-wrap/SKILL.md) — uses `validate_continuity_size.py` pre-commit hook as structural enforcement of compression discipline; `validate_continuity_evidence.py` enforces pattern-graduation grounding structurally
- [`flow-flowscript-encoding`](../flow-flowscript-encoding/SKILL.md) — required-field markers ([decided(rationale, on)] etc.) ARE structural invariants; you cannot encode without the required fields
- [`flow-forever-rules`](../flow-forever-rules/SKILL.md) — structural protection of identity-layer rules; pre-commit hooks and code invariants instead of pure discipline
- [`flow-sourdough-scoping`](../flow-sourdough-scoping/SKILL.md) — scoping-as-structural-mechanism beats scoping-as-willpower (sibling pattern at methodology layer)

The pattern generalizes: **wherever discipline is being used to maintain a property, look for the structural mechanism that maintains the property without depending on discipline.**

## Common architectural surfaces where this applies

- **Database migrations** — schema constraints over application-level checks
- **API contracts** — type systems and schema validation over docs
- **Concurrency control** — locks and atomic operations over "be careful"
- **State machines** — explicit transitions over implicit state
- **Build systems** — required tests and hooks over "remember to run X"
- **Identity files** — structural protection of identity-layer rules
- **Memory architectures** — graduation guards, immune system, pre-commit validation
- **Configuration systems** — schema enforcement at load time over runtime hopes

## Reference

This skill is self-contained — the principle is conceptually clean, value is in the disciplined application across many concrete surfaces.

Related skills:
- [`flow-session-wrap`](../flow-session-wrap/SKILL.md) — applies pattern to memory architecture (graduation guards + compression invariants)
- [`flow-flowscript-encoding`](../flow-flowscript-encoding/SKILL.md) — required-fields as structural invariant
- [`flow-forever-rules`](../flow-forever-rules/SKILL.md) — structural protection of identity-layer rules
- [`flow-sourdough-scoping`](../flow-sourdough-scoping/SKILL.md) — sibling pattern at methodology layer

## Provenance

Graduated to Proven in FLOW methodology after 11+ operational firings across heterogeneous substrates (Apr-May 2026):

- Code substrate: anneal-memory parity test for cross-transport equivalence, CAS pattern for save-continuity verification, scheduler prune-merge known-set fix, db_committed phase flag in two-phase commit
- Flow-meta substrate: STEP 1.5 episodic grounding gate in wrap procedure, seal-self-defeat invariants in continuity validation
- Audit substrate: chain-of-custody via token, evidence annotation pre-commit hook
- Scheduler coordination substrate: prune-merge with explicit known-set
- Continuity-shape substrate: three structurally-enforced budgets (State ≤4k / Developing block ≤15L / total ≤140k) via `scripts/validate_continuity_size.py`

The pattern is the foundational discipline-to-structure conversion that makes FLOW methodology load-bearing at production scale rather than failing under operator-load drift.

Parent lineage: forcing_functions > willpower (2x pre-episodic). Sibling family includes review-is-structurally-required (multi-dimensional review at boundary moments) and sourdough-scoping (commit-shape vs derive-theorem).
