# Related operations, classes, and independent functions

A retrieval implementation has separate modules for search, inspection, and
expansion. Each function receives the same owner, repeats scope handling, and
participates in the same result-assembly rules. The files are individually small,
but one change requires reconstructing their shared context.

## Reason through the boundary

Compare purpose, dependencies, invariants, and lifetime together. Repeated
parameters suggest a relationship; they do not prove that operations belong in
one class. Here the operations jointly provide bounded reads over one authorized
scope, so one query component can bind storage and embeddings once. Its methods
then accept only the inputs specific to an operation:

```text
GraphQueries(scoped_storage, embeddings)
  .search(request)
  .inspect(ids, view)
  .expand(ids, filters)
```

Shared mutable state is not a prerequisite for that class. The benefit is a
coherent owner for the read rules and collaborators. Conversely, putting each
function into its own class would preserve the fragmentation with extra ceremony.

Keep independent concerns understandable on their own. A cache can own eviction
and snapshot isolation; a path algorithm can accept a narrow adjacency callback;
a formatting function can transform already-loaded results without I/O. These
boundaries earn their separation through different rules, not their line counts.

## Tradeoffs and limits

Grouping operations reduces repeated wiring, but an overly broad class hides
unrelated responsibilities. Sharing a database alone is insufficient reason to
merge components. Pure transformations and small stateless modules can remain
functions. An explicit callback can be a sound dependency boundary; passing a
whole parent object merely to recover its collaborators usually is not. Preserve
resource and transaction lifetimes when regrouping the implementation.
