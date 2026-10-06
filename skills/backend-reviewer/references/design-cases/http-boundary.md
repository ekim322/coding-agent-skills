# Reviewing the HTTP boundary

A project calls for HTTP endpoints under `api/<capability>/`, with application
operations usable by other callers. The code has `workspace/routes.py` and
`auth/routes.py`. A previous review described the handlers as thin and endorsed
the layout.

## Establish the evidence

Assess placement and contents independently. Trace app router registration,
shared HTTP dependencies, the handler body, and the application operation it
invokes. Determine which checks belong to transport and which must also hold for
jobs or CLI callers. A short handler can still be misplaced, and a correctly
placed handler can still own application policy.

```text
Observed placement:  workspace/routes.py
Intended adapter:    api/workspace/<endpoint group>.py
Application owner:   workspace/<relevant operation>
```

With that project convention established, a placement finding can name the route,
the intended adapter boundary, and the navigation/dependency consequence without
claiming a runtime bug. Separately substantiate any business-policy finding with
the specific processing or invariant that HTTP currently owns. A correction
should preserve a single application authority for that behavior.

## Evidence that changes the conclusion

A project explicitly organized around feature-local adapters may reasonably keep
transport near its capability. That requires a different placement assessment,
while the dependency and behavior checks still apply. Existing policy-free
handlers should not acquire forwarding services solely to match an example.
Do not turn the preferred `api/` convention into proof that every other layout is
wrong, or count route lines as a measure of separation. The skill's convention
applies absent an explicit alternative; distinguish that convention from a
behavioral defect in the report.
