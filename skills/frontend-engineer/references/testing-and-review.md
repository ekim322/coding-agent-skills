# Testing and Review

## Verify the behavior that could fail

Use the lowest test level that exercises the real risk: unit tests for pure rules,
component tests for interactions and state, integration tests for queries and
mutations, and browser tests for navigation and critical journeys. Follow project
requirements and use existing tools. Cosmetic changes need targeted inspection,
not new tests that mirror the edit.

Assert user-visible outcomes and meaningful invariants through roles, labels,
and public contracts. Avoid private-state assertions, arbitrary sleeps, and
snapshots coupled to incidental markup. Control response timing and failures
without mocking away the behavior under test. Keep tests isolated and repeatable.

Choose cases from the change's risks: stale responses, failed mutations, rapid
navigation, denied access, draft loss, and session/cache isolation. For a bug,
add regression coverage when practical and establish that it detects the failure.
Run focused checks first and broaden for required gates or unresolved concerns.
Report pre-existing failures, environment limits, and unrun checks accurately.

Reference: [Playwright testing practices](https://playwright.dev/docs/best-practices).

## Exercise the rendered application

For UI or browser behavior changes, run the affected journey with available
authorized browser tooling. Inspect representative sizes and realistic content,
including relevant loading, empty, and error states. Verify navigation, keyboard,
focus, submission, and recovery where affected; inspect console and network output.
A screenshot does not prove an interaction works, and compilation does not prove
a usable layout.

Compare appearance against the requested design or established visual system.
Check overflow, text wrapping, density, and overlays. For shared styles or
components, inspect representative other consumers and variants. Use computed
styles for suspected cascade issues and route transitions for style leakage.
Capture only evidence that helps assess the result, with private data redacted.

State when fixtures or mocked APIs limit the verified scope. If browser/backend
access is unavailable, complete feasible checks without claiming full integration
or rendered verification.

## Review structure separately from behavior

Inspect the resulting package tree, imports, state flow, and affected consumers,
not only the diff. Compare actual ownership with project intent. A working screen
can still have deficient component or feature boundaries.

- Can an engineer locate a feature's behavior without following unrelated code?
  Are route composition, feature policy, shared UI, and data access distinguishable?
- Does decomposition follow responsibilities, or merely move complexity into a
  large hook, generic component, or catch-all module? Is nesting meaningful?
- Does each state value have a clear authority and lifetime? Can effects, caches,
  or duplicated state disagree during navigation, refetch, or identity changes?
- Can styles leak across features or couple consumers to private markup? Does a
  shared change preserve its other consumers and interaction contracts?
- Can users complete the journey across relevant failure states and input methods?
  Could a wrapper, effect, state variable, or dependency be removed safely?

Support structural findings with concrete ownership/dependency evidence and name
the intended correction. Do not endorse architecture solely because tests pass.
For substantial or high-risk work, use a fresh read-only reviewer when available
and permitted; otherwise conduct a separate self-review and describe it accurately.
Give an independent reviewer the task and artifacts without an expected conclusion.

Resolve material findings within scope and rerun affected checks. Finish with the
resulting behavior, verification evidence, and remaining limitations. Update
changed contracts or operating instructions without adding unrelated work.
