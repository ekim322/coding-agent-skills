---
name: frontend-engineering
description: >-
  Designs, implements, debugs, refactors, and reviews frontend applications,
  especially React and TypeScript. Use for pages, components, forms, routing,
  frontend state and API integration, responsive styling, accessibility,
  browser performance, and frontend tests. Applies to the frontend portion of
  full-stack work; does not cover backend-only implementation or standalone
  graphic design.
---

# Frontend Engineering

Build interfaces whose behavior, state, and code are easy to understand and
extend. Treat usability, accessibility, and runtime correctness as part of the
implementation. Respect the requested scope; review or design does not authorize
application changes or unrelated redesigns.

## Mental model

Reason from the user journey to ownership, then to code structure:

- **Ownership:** Which feature owns the behavior, data, state, and styles?
- **Boundaries:** What belongs to route composition, feature logic, reusable UI,
  data access, or the server? What should each consumer need to know?
- **Decomposition:** Which responsibilities change together, and which deserve
  independent components, modules, or subpackages?
- **Contracts:** What props, events, state transitions, and loading/failure
  outcomes must remain coherent as the user interacts with the application?

Neither flat files nor deep nesting are a goal. Make the real responsibilities
visible without turning every component into a framework.

## Working approach

Read project instructions, product/design context, installed framework versions,
and the owning code, callers, and tests. Trace the relevant user journey and its
state/data flow. Reproduce a bug or establish a concrete failure path. For a
substantial change, briefly identify owners and boundaries; local fixes need no
architecture report.

Implement a coherent change within scope. Existing patterns are evidence to
assess, not automatic authority. Reuse sound design-system and framework
conventions; explain necessary departures. Avoid speculative abstractions,
parallel state systems, and unrelated dependency upgrades.

For rendered UI or browser behavior changes, exercise the affected journey with
available authorized browser tooling. Inspect relevant visual and interaction
states, console output, and network failures. Run appropriate repository checks,
then review both the resulting structure and behavior.

Report what changed, checks actually run, and material limitations. Distinguish
compilation, mocked tests, browser inspection, and real integration evidence.
If the application cannot be exercised, perform feasible checks and state the gap.

## References

Resolve these paths relative to this skill. Read applicable references once.

| Reference | When to read |
| --- | --- |
| [Engineering standards](references/engineering-standards.md) | Designing, implementing, or reviewing frontend code |
| [Production quality](references/production-quality.md) | Relevant sections for UI, interaction, browser runtime, accessibility, or security changes |
| [Testing and review](references/testing-and-review.md) | Choosing verification or reviewing a change |
| [Glass material](references/glass-material.md) | Creating or refining a convincing glass surface, especially over pale backgrounds |
