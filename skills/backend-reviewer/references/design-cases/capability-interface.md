# Reviewing a public capability and its internals

A package exports `KnowledgeGraph`, while sibling modules implement query
assembly, path finding, record caching, and text output. Callers may already use
the exported class correctly. That answers the public-usage question, but does
not settle whether the internal organization communicates ownership.

## Establish the evidence

Trace an ordinary caller to its operation, then locate the implementation and its
collaborators from the package structure. Evaluate how a maintainer would find
and change that operation. Identify specific ambiguous owners or scattered
responsibilities; a count of sibling files is not sufficient evidence.

Inspect the passed objects as well as imports. For example:

```text
Public graph → query helper(graph) → public graph → storage
```

This path warrants examining whether the helper has an independent contract or
merely relocates its owner's method body. Type-only imports can conceal this
runtime dependency from an import-cycle check. A narrow storage dependency can
clarify it, while grouping internal query collaborators can clarify navigation.
Those are separate corrections; moving files alone does not change dependencies.

## Evidence that changes the conclusion

A retrieval owner is coherent when fetching, ranking, pagination and assembly
serve a shared result contract. Exploration can deserve a distinct owner when
investigation introduces its own rules and results; visualization can deserve
one when geometry, layout and viewport contracts change independently. Check
that those responsibilities exist in the code before recommending a split.
A thin view over fetched records may need neither new component. Shared storage
access can remain common without merging the capabilities it supports.

Likewise, editing that reads snapshots to enforce revision and evidence rules
can remain cohesive. Trace the required read contract before calling it coupled
to exploration. Shared reconciliation policy, mutual knowledge of internals or
interleaved state transitions would be stronger grounds to reconsider ownership.
Use a concrete change to compare the alternatives; a rename alone proves none
of these improvements.

A small public module with local helpers can already be clear. Multiple public
interfaces can also be deliberate when they serve different callers and stable
contracts. Support a finding with the responsibility being obscured and the
practical cost, then name the owning component or package. Do not demand one
public class, hide supported setup/model interfaces, or introduce a nested
package just to match this case. A narrow injected callback with a clear contract
can be useful; the concern is dependence on an owner's internals.
