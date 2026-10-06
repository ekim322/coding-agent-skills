# Testing and Review

## Verify the behavior that could be wrong

Choose the lowest test level that exercises the real risk. Use focused unit tests
for rules and transformations, HTTP tests for request/response contracts, database
integration tests for storage semantics, and workflow tests for coordination and
recovery. Follow project verification requirements and scale checks to the change.

- For a bug, establish the failure and add a regression test when practical.
  Assert outcomes and invariants rather than helper structure or file layout.
- Mock external boundaries, not the internal behavior under test. Fakes do not
  establish real locking, isolation, provider behavior, or production capacity.
- Select failure cases from the operation's contract: denied access, invalid or
  absent inputs, partial failure, duplicates, timeout, cancellation, and recovery.
  Do not mechanically add every case to every change.
- Make concurrency checks deterministic with barriers or controlled time instead
  of sleeps. Verify the effects and limits that concurrent execution must preserve.
- Run focused checks first; broaden for required gates or unresolved risks.
  Distinguish pre-existing failures and environment limits from regressions.
  Avoid new tests for cosmetic edits or tests that merely mirror implementation.

## Review the resulting design

Inspect the completed design from a caller's and a maintainer's perspective,
including surrounding code, actual usage, imports, and the package tree. Reassess
the whole boundary after restructuring; local improvements can still leave an
incoherent result. Ask:

- Is the supported entry point discoverable, with internal collaborators visibly
  owned beneath it? Do normal callers need to understand implementation choices?
- Do operations that share dependencies and rules have a cohesive owner? Does
  each extracted component reduce what a maintainer must understand, or merely
  spread one responsibility across more locations?
- Do runtime calls and passed objects respect the intended dependency direction,
  as well as imports? Can a component be understood through its declared contract
  without reconstructing its caller? Check application and adapter separation.
- Are outcomes correct under the relevant failures, concurrent operations, and
  trust boundaries? Are existing contracts preserved or deliberately migrated?
- Can an abstraction, wrapper, branch, or configuration option be removed without
  losing useful behavior? Is either a broad file or excessive nesting obscuring
  responsibilities?

Behavioral verification and design assessment are separate obligations. Tests
establish behavior; traceable ownership, discoverable interfaces, and localized
change establish structural quality. File counts and clearer names prove neither.

For substantial or high-risk changes, use a fresh read-only reviewer when
subagents are available and permitted, applying [backend-reviewer](../../backend-reviewer/SKILL.md)
when available. Provide the request, constraints, and artifacts without supplying
an expected conclusion. Otherwise perform a separate self-review and describe it
accurately. Require an independent assessment of the chosen decomposition, not
just confirmation that the moved code behaves the same.

Resolve material findings within scope and rerun affected checks. Finish when the
requested behavior is implemented, required verification is complete or its limits
are disclosed, and the change has no unrelated edits. Update affected contracts
and operating instructions; report checks actually run and remaining risks.
