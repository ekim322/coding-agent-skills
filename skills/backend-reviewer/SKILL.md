---
name: backend-reviewer
description: >-
  Independently reviews backend structure, maintainability, correctness, and
  reliability. Use for backend code reviews, architecture assessments, and
  review of backend changes. Reconstructs actual responsibilities and dependencies
  to identify design deficiencies as well as bugs. Read-only unless fixes are
  separately requested; excludes frontend-only reviews.
---

# Backend Reviewer

Determine where a backend's design or behavior falls short, using evidence from
its code. Evaluate the architecture on its own merits before accepting local
implementation choices. This skill is self-contained; it does not depend on an
implementation skill or its assessment.

## Reconstruct before judging

Read the request, exclusions, project instructions, and relevant design documents.
Separate descriptions of what exists from decisions about what should exist.
Begin with the caller's view: identify the supported imports, constructed objects,
and operations from actual usage. Compare that public surface with what the package
structure presents. Then trace representative operations through application
decisions, persistence, and external calls. For a change review, include affected
callers and surrounding boundaries; the proposed decomposition is a hypothesis
to assess, not the frame the review must accept.

Build a compact responsibility map: public entry points, internal owners,
collaborators, locations, and dependency direction. Include runtime calls and
passed objects; imports alone can hide coupling. Derive intended ownership from
requirements and usage, then compare it with the implementation. A filename is
not proof of responsibility. Respect excluded areas and identify review limits.

## Challenge the boundaries

Use these criteria alongside explicit project requirements. Explain departures
rather than silently accepting them or imposing a universal directory template.

When multiple groupings are plausible, assess the existing arrangement as well
as the proposed split or consolidation. Distinguish shared mechanisms from
shared purposes using actual contracts and a likely change. A workflow reading
another component's data is not by itself entanglement. State what evidence
would favor keeping the components together; names and example trees are not
evidence that a boundary is sound.

- **Public surface and package structure:** Assess normal usage and implementation
  navigation separately. Clear exports do not establish clear internal ownership.
  Check that callers can identify how to use the capability and maintainers can
  locate an operation's owner and collaborators from the package structure. Compare
  placement, exports, names, and actual usage; group implementation beneath its
  owner without imposing a universal depth or number of entry points. A correct
  call graph can still be presented through a confusing package structure.
- **Transport separation:** HTTP belongs under `api/<capability>/`, grouped by
  endpoint area. Application processing belongs in its capability package.
  Placement and handler contents must both respect that boundary. Shared HTTP
  dependencies should not require another feature's endpoint module.
- **Cohesion and decomposition:** Evaluate which operations share a purpose,
  dependencies, invariants, and lifetime. Their grouping should localize reasoning
  and change. Classes can express this cohesion without mutable state; functions
  can express independent operations. Assess the ownership achieved, not the syntax
  or file size. Fragmentation and unrelated concerns in one component both obscure
  responsibility; do not prescribe a class or file per operation or layer.
- **Method readability:** Can a reader understand the operation's main steps
  without mentally executing its loops, filtering conditions, and result
  construction? Check that public operations express the workflow and substantial
  machinery sits in intent-revealing private methods, on the same class when it
  shares that owner. Flag nested conditional expressions or dense comprehensions
  when they obscure decisions; prefer explicit branches and named intermediate
  values. Assess whether helpers hide meaningful complexity or merely force
  navigation through trivial wrappers. Neither method length nor helper count
  alone establishes a finding; identify the obscured step and a concrete clearer
  arrangement.
- **Dependency direction:** Application rules should not require HTTP objects or
  vendor/storage representations. Reusable machinery should not know application
  workflows. Follow delegation through to its implementation: a component should
  be understandable through its dependency contracts without reconstructing its
  caller. Check whether a boundary reduces knowledge or merely relocates code.
  Find runtime and import cycles, private cross-package access, incidental
  re-exports, and duplicated rules; establish consequences before adding wrappers.
- **Contracts and lifecycle:** Identify who owns validation, state transitions,
  transaction scope, clients, and tasks. Check whether alternate callers preserve
  the same business invariants and permissions. Locate hidden globals, ambiguous
  outcomes, and resources whose construction or cleanup has no clear owner.

Test the design with a realistic extension: another entry point, source adapter,
or operation relevant to the product. Trace what would change and why. Use actual
coupling as evidence; do not invent requirements to justify abstractions.

## Review file and folder names

Assess names explicitly alongside the responsibility map. From the directory
listing and full import paths, can a maintainer locate connection management,
file listing, or another relevant workflow without opening multiple candidate
files? Check whether a module's name describes what its implementation owns.
Common naming practice is supporting context, not proof of clarity.

Scrutinize generic names such as `service.py`, `provider.py`, `manager.py`, and
`utils.py`. They can be adequate when package context makes the responsibility
clear; otherwise identify the obscured responsibility and suggest a concrete
name. Retain conventional entry-point and configuration names when useful. Do
not demand verbose names, repeated package context, or a universal folder tree.

Report a naming deficiency when it creates concrete navigation ambiguity, even
if runtime behavior and internal boundaries are sound. Distinguish a rename from
a necessary responsibility split: unclear names alone do not justify new layers
or modules. Account for imports and public compatibility in the suggested fix.

## Examine failure paths

Check the relevant risks: authorization and tenant isolation, mismatched validation
limits, competing writes, partial commits, unsafe retries, cancellation, unbounded
work, and lost diagnostics. Timeouts can leave mutation outcomes uncertain;
application checks alone may not prevent races. Verify transaction and idempotency
claims against their implementation.

Inspect existing tests and use focused, non-destructive checks when useful. Fakes
cannot establish actual database isolation or provider behavior. Distinguish
reproduced defects, conclusions supported by code, and unresolved risks. Passing
tests do not answer whether the package structure is sound.

## Review observability and queryability

For changed requests, jobs, dependencies, and application stages, check whether a
reported failure or slowdown can be traced to an execution and explained. Read
the repository's observability guide and follow inherited instrumentation before
claiming a missing log; binding context attaches fields but emits no records.
Do not demand instrumentation on every helper or expand a review into a vendor
migration. Assess these contracts within the reviewed scope:

- Meaningful events use stable names and structured fields. Spans expose relevant
  stage/dependency timing; metrics expose aggregate rates and latency. Flag a
  missing stage only when its absence creates a concrete diagnostic blind spot.
- Request/trace IDs and durable domain/run/job IDs connect the affected work.
  Async contexts stay isolated; queued work retains correlation across restart.
  In-process inheritance alone does not establish cross-process trace propagation.
- Outcomes include handled errors, cancellation, retries, and persistence failures.
  Successful admission is distinguishable from successful execution. Repeated
  logging of the same failure and noisy polling should not bury useful evidence.
- Metric labels have bounded cardinality; units and histogram buckets support
  the intended latency questions. Unique identifiers belong in logs/traces.
- Secrets, raw user/model payloads, and sensitive URLs are omitted; arbitrary
  free text is not assumed safe because field names are redacted. Optional export
  has bounded resource use, lifecycle ownership, and failure containment.
- A documented query path can narrow by environment/time/identity, connect logs
  to traces, and move from aggregate symptoms to individual executions. OTLP
  export alone does not establish storage, retention, query access, or dashboards.
  Queries should bound output and expose truncation; absent evidence is not proof
  of success when collection, sampling, or retention is incomplete.

Distinguish emitted-field tests from actual export and retrieval evidence. Use
existing authorized test tools when useful, without requiring a production query
or live provider call for every review. Report concrete diagnostic gaps with the
affected failure path and a scoped correction; keep optional enhancements separate.
This remains a read-only assessment unless implementation is requested.

## Deliver a defensible assessment

Report structural deficiencies separately from behavioral defects, prioritized by
consequence. For each material finding, identify the actual location/dependency,
the expected responsibility or convention, its practical impact, and a concrete
correction. For misplaced code, name the intended owner or destination. Structural
findings need evidence of a boundary or convention mismatch, not a runtime crash.
Distinguish an explicit requirement from a preferred convention or optional idea.

Before endorsing the architecture, reconcile the whole responsibility map with
the criteria above. Trace normal usage and a likely maintenance change through
the final arrangement: the caller should see a clear capability, and the maintainer
should find a coherent owner. Justify the remaining indirection; preserved behavior
and clearer names alone do not establish its value. Treat avoidable navigation,
exposed implementation choices, and scattered ownership as structural evidence,
not merely taste. Do not substitute a bug list for a requested structure review
or generalize a few good components into an endorsement of the whole backend.
State the inspected scope, verification, and unknowns. Do not manufacture findings
or implement proposed corrections without authorization.

## Design cases

Read the relevant case for the boundary under review. Each demonstrates how to
establish or reject a structural finding from evidence; none supplies an expected
verdict or a required folder tree. These references are local to this skill.

| Case | When to read |
| --- | --- |
| [HTTP boundary](references/design-cases/http-boundary.md) | Reviewing route placement and application/transport separation |
| [Public capability and internals](references/design-cases/capability-interface.md) | Reviewing discoverability, ownership, or delegation boundaries |
| [Component cohesion](references/design-cases/cohesive-components.md) | Reviewing fragmentation, consolidation, or class/function choices |
