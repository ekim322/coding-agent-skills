# Engineering Standards

## Structure follows responsibility

Organize application behavior by capability. Keep its rules, operations, contracts,
and domain-specific persistence discoverable together. Separate HTTP handling and
reusable technical machinery at clear boundaries. Focused packages such as `api/`,
`llm/`, and `clients/` can coexist with capability packages; an `infrastructure/`
parent is useful only if it makes navigation clearer.

Make the public surface apparent in each package's structure, exports, and actual
caller usage. Group internal collaborators beneath their capability owner so
implementation details do not appear to be competing entry points. Assess normal
usage and implementation navigation separately: clear imports alone do not make
internal ownership easy to follow. A maintainer should be able to locate an
operation's owner and its collaborators from the package structure. Documentation
should explain that structure, not compensate for a misleading one.

Use a module for a cohesive responsibility and a subpackage for a meaningful group
of responsibilities. Weigh the independence gained by a split against the extra
concepts and navigation it introduces. Names should explain ownership and purpose;
choose depth by those relationships rather than imposing a fixed tree, file size,
or one-file-per-operation convention.

Follow explicit project conventions and user preferences. When establishing a
new convention, record it in project architecture docs. A task involving one
boundary does not authorize reorganizing the rest of the repository.

## Dependencies follow boundaries

Application rules should be usable without an HTTP server, a vendor SDK's data
objects, or knowledge of physical database representation. Adapters translate
between application contracts and external systems; composition code constructs
and connects the concrete dependencies. Reusable machinery must not depend on
application-specific workflows.

Put shared concepts and interfaces with their owner. Multiple consumers do not
make a type an application-root module. Domain-specific storage remains part of
its capability even when its implementation is named after a database. Share an
abstraction when it captures a stable concept, not merely similar-looking code.

Expose deliberate public interfaces and avoid cycles, cross-package private
imports, and imports through incidental re-exports. Trace runtime calls and passed
objects as well as imports. Bind a delegated component to the capabilities it
uses, with a contract that reveals those dependencies. An extraction that still
requires understanding its caller's internals has not reduced that coupling.
Public coordination can hide useful implementation complexity, but each internal
boundary must isolate real responsibility. Introduce a protocol or adapter for a
useful substitution or testing boundary, not to legitimize unnecessary indirection.

## HTTP is an adapter

Group HTTP endpoints under `api/<capability>/`, split by cohesive endpoint
area as they grow. Keep application behavior in the corresponding capability
package; placing `routes.py` directly in that package does not establish this
separate API boundary. An explicit alternative project convention takes precedence.
Keep request/response schemas and HTTP dependencies at that boundary. Share authentication and injection dependencies through focused modules
rather than importing them from another endpoint module.

Handlers parse requests, obtain authenticated context, call application
operations, and translate results and errors to HTTP. Processing and business
rules belong in the owning capability, decomposed as its complexity warrants.
That code must not import `api/` or require `Request`, `Depends`, or
`HTTPException`. A handler can call a capability function directly; a service
class is not mandatory.

Validate request shape and transport requirements in the API. Enforce resource
permissions, state transitions, consistency, and business limits in application
operations so CLI and background callers receive the same guarantees. A valid
identifier is not proof of ownership. Pass identity and other dependencies
explicitly rather than reaching into request state from application code.

## Contracts express meaning

Use meaningful types for inputs, outputs, units, identity, and failure outcomes.
Distinguish domain models, consumer/query contracts, and stored representations
when their responsibilities differ. Keep driver shapes, storage versions, and
physical mappings inside persistence. Reuse a type when it fits; do not duplicate
every model at every boundary.

Use Pydantic for runtime validation and serialization of untrusted inputs or
configuration. For trusted internal values, ordinary typed Python structures are
often sufficient. Choose coercion, unknown-field behavior, and nullability
intentionally. Use timezone-aware datetimes and suitable decimal or integer
representations where monetary precision matters.

Treat consumed interfaces, schemas, events, and configuration as contracts. Check
callers before changing them and coordinate breaking changes or migrations.
Distinguish absence, partial results, rejection, and failure. Do not return empty
success for a failed dependency. Translate specific exceptions at their owning
boundary, preserve diagnostic causes, and keep sensitive details out of public
responses. Catch errors where recovery, translation, or containment is possible.

## Keep mechanics explicit

Pass dependencies explicitly and give clients, pools, and tasks an identifiable
lifecycle owner. Construct shared resources at startup and close them at shutdown;
avoid import-time network connections and hidden mutable globals.

Choose functions and classes by cohesion and dependency ownership, not mutability
alone. Related operations with a stable set of collaborators and shared rules can
form a useful class; independent transformations need no object wrapper. Bind
component dependencies at construction and pass operation-specific inputs at the
call. Judge the result by whether it localizes reasoning and change, rather than
by method counts or shorter files.

Keep configuration validated and its source clear. Keep control flow direct,
use existing tooling, and comment on non-obvious constraints or tradeoffs rather
than narrating code. Optimize measured bottlenecks rather than adding mechanisms
for hypothetical future load.
