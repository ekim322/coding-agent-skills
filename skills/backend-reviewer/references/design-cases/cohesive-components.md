# Reviewing cohesion across functions and classes

Several query modules accept the same graph object and implement related bounded
reads. A proposed cleanup renames them or wraps each function in a class, while
leaving their shared rules and dependencies distributed.

## Establish the evidence

Map operations to their purposes, collaborators, invariants, and lifetimes. Trace
a plausible change through the code: where would a scope rule or result-assembly
policy have to change, and who would own that decision? Repeated wiring, scattered
rules, or a dependency on the caller can establish a cohesion problem. Merely
having multiple functions cannot.

A class can bind related operations to collaborators even without mutable state.
A module can group independent functions without object ceremony. Evaluate the
responsibility achieved by either choice; adding methods does not automatically
improve a boundary. The comparison is between owners and contracts, not syntax.

```text
Useful shared owner:   related reads over the same authorized storage scope
Independent concern:   cache eviction and snapshot reuse
Independent concern:   path finding through a narrow adjacency interface
```

A supported finding identifies which operations share a responsibility, where
that responsibility is currently fragmented, and how a smaller set of components
would localize reasoning. It should also identify independent concerns that the
proposed consolidation should leave separate.

## Evidence that changes the conclusion

Distinct algorithms or transformations can deserve separate modules despite
sharing inputs or a database. Separate resource or transaction lifetimes can
also justify separate components. Conversely, a large class can mix concerns
just as easily as a directory of tiny helpers can scatter them. Do not require a
class per operation, merge by parameter similarity, or infer good design from
file size, passing tests, or new names alone.

## Review boundaries after growth

Consider a small ingestion owner that originally handled listing, reconciliation,
revision publication, and retries. A proposed extension adds source adapters or
retention behavior. Reconstruct the affected responsibilities from actual callers
and contracts; do not accept the original file boundary or the proposed new files
as evidence that ownership is correct.

Trace an import and a relevant maintenance change. If a new source requires
editing unrelated retention rules, inspect why those responsibilities are coupled.
If reconciliation and publication must preserve one transaction invariant, a
split that hides their coordination may make the design worse. If a nested package
contains only forwarding wrappers, identify the added navigation rather than
endorsing its appearance. Conversely, a flat list of source, scheduling, and
history modules may obscure groups with independently understandable contracts.

Support a finding with the actual scattered rule, hidden responsibility, or
dependency that makes the change harder to understand. Explain whether clearer
names, private methods, module regrouping, or a coherent subpackage would address
it, and what favors retaining the current arrangement. Distinguish corrections
within the reviewed change from broader options. File counts, hypothetical future
features, and individually clean methods do not establish the verdict.
