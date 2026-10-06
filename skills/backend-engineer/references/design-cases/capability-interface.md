# A public capability and its internal implementation

Callers use `KnowledgeGraph` to save, search, inspect, and traverse knowledge.
Search preparation, path finding, caching, and result formatting support that
interface, but their placement and names make them look like alternative entry
points. Correct imports alone do not make the implementation easy to navigate.

## Reason through the boundary

Start with normal usage: callers obtain an authorized graph and invoke its
operations. Keep that interface stable while identifying the purposes and rules
of the machinery that serves it. Similar operations can warrant different
groupings in different systems:

| Evidence in the implementation and caller needs | Plausible ownership |
| --- | --- |
| Search, filtering, pagination and result assembly jointly fulfill a bounded knowledge-fetching contract, with shared relevance and completeness rules | A retrieval component can own these operations together |
| Entity resolution, evidence inspection and connection investigation support discovery, with investigation-specific results and rules beyond fetching records | An exploration component can own that behavior and consume retrieval/storage capabilities |
| Viewport selection, geometry and layout produce a visual graph representation, with contracts that change when the visual experience changes | A visualization component can own those projections and algorithms |
| A small implementation only fetches records for a view, with no independent investigation or layout policy | Keep it together; capability names alone do not justify new components |

Test the alternatives with a realistic change. Would changing layout rules touch
evidence inspection? Would changing relevance or pagination require coordinated
changes across all query operations? Locate the owner of each rule and the
contracts between owners. Shared low-level reads can support distinct purposes;
duplicating them into each capability is not required. Separate modules inside
one component may be sufficient when independent packages would add navigation
without making responsibilities easier to understand.

An edit workflow may read snapshots to validate revisions and evidence. That
dependency does not itself merge editing with exploration. Check whether it
needs a narrow record lookup or actually shares reconciliation rules, state and
lifetime with investigation. Bidirectional knowledge or duplicated policy is
stronger evidence of entanglement than both workflows reading the same graph.

Now check the runtime path independently of this tree. Delegating to a helper
that receives the whole graph and calls back into it still requires understanding
the helper's owner. Bind query operations to the scoped storage and embedding
capabilities they use instead:

```text
Caller → KnowledgeGraph → internal query component → scoped storage
```

The public class coordinates lifetimes and hides implementation choices; the query
component owns the identified query rules and result assembly. Storage owns physical reads.
The placement explains ownership, and the dependency contracts make it real.

## Tradeoffs and limits

A public facade is useful when it hides a coherent capability's complexity. Do
not add one merely to forward an unrelated collection of methods. Small
implementations can stay in the public module; substantial independent machinery
can justify internal components. The example does not require a single public
class everywhere or a `retrieval/` directory by name. Preserve deliberate model,
contract, and setup interfaces; internal grouping is not a reason to break them.
