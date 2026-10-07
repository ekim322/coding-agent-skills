# Engineering Standards

## Structure follows responsibility

Organize application behavior by feature. Keep feature-specific UI, state logic,
data adapters, and styles discoverable together. Route entry points compose
features and handle navigation/framework integration; reusable UI primitives own
interaction and visual contracts without importing feature policy. Framework
routing conventions may constrain file locations without determining where all
application behavior must live.

Choose file and folder names that help maintainers find both the subject and,
when it is otherwise unclear, the component's role. Names such as
`ProfileForm.tsx`, `useProfile.ts`, and `profileApi.ts` can distinguish UI,
stateful behavior, and data access. Use the role suffix only when it adds useful
information; established framework conventions and an obvious feature context
may already make the role clear. Avoid generic names such as `service.ts` or
`utils.ts` when they hide materially different responsibilities, and avoid
encoding incidental implementation details in names. Do not impose one naming
scheme or folder tree across unrelated features.

Decompose recursively as responsibilities emerge. A complex feature can contain
several cohesive subpackages; do not force it into a flat component/hook/service
trio. Split when independent concerns obscure each other, not at a line limit.
Avoid both monolithic screens and pass-through component chains. Global
`components/`, `hooks/`, or `utils/` collections should not become unrelated
feature storage.

Keep imports directed through deliberate interfaces. Shared UI must not depend
on features, and features should not reach into each other's private internals.
Put rules with their owner instead of repeating them in rendering, fetch
callbacks, and event handlers. Shared abstractions should capture stable behavior,
not only visual resemblance. Follow explicit project conventions and user choices;
identify structural gaps without expanding a local task into a repository rewrite.

## Components express UI contracts

Give components cohesive responsibilities and explicit typed props and events.
Prefer composition over a growing set of unrelated mode flags. Components compose
UI, hooks encapsulate cohesive stateful behavior, and plain functions transform
data or express rules. Moving an entire screen into one enormous hook does not
improve decomposition. Do not require a hook for every helper or request.

Keep rendering pure. Handle user actions in event handlers and use effects to
synchronize with external systems. Derive values during rendering instead of
synchronizing duplicate state through effects. Declare dependencies honestly and
clean up listeners, timers, observers, subscriptions, and obsolete requests.
Repeated setup/cleanup must be safe. Use stable identity for list keys and make
state preservation or reset intentional when an entity or route changes.

## State needs an authority and a lifetime

Choose an owner by meaning, rather than convenience:

| State | Usual owner |
| --- | --- |
| Remote records and request status | Existing framework loader or query/cache layer |
| Temporary interaction | Nearest component or cohesive feature boundary |
| Editable draft | Form boundary with explicit initialization and reset rules |
| Shareable view, filter, or pagination | URL when navigation semantics warrant it |
| Application-wide client state | Focused shared store/context when actual consumers require it |

Keep one authoritative source for each fact and derive dependent values. Avoid
mirroring remote objects into local state unless a draft or snapshot is intended.
For drafts, define dirty state, refetch conflicts, entity changes, and recovery.
Represent mutually exclusive states explicitly rather than allowing contradictory
loading/success/error flags. Keep state local until sharing has a concrete owner;
consider subscription scope when introducing context or a global store.

Preserve deep links and back/forward behavior. Distinguish transient input from
committed navigation state. User/session changes must not retain another user's
sensitive state or allow an old response to overwrite the new view.

## Data access has explicit contracts

Use the existing loading/cache mechanisms rather than creating parallel fetch
effects. Keep transport details and external representations behind useful feature
data boundaries; do not invent endpoints or introduce an adapter per function.
Type inputs, results, and errors, reusing generated contracts where available.
Validate untrusted payloads when runtime guarantees are needed: a TypeScript cast
cannot validate JSON. Keep units, currency, time zones, freshness, and missing
values explicit; unknown is not zero.

Define query identity, freshness, invalidation, and user/tenant scope. Prevent late
responses from overriding newer intent, with cancellation and/or result identity
checks. Mutations need pending, failure, and reconciliation behavior. Optimistic
updates require rollback that remains correct under concurrent mutations. Keep
sensitive caches segregated or cleared when identity changes.

## Styles have owners too

Reuse the design system, tokens, and established interactions. Scope feature and
component styles using the project's mechanism; separate CSS files alone do not
isolate selectors. Reserve globals for deliberate foundations and theme tokens.
Parents own surrounding layout; children expose supported variants or styling
contracts instead of requiring consumers to target private DOM structure.

Keep selectors shallow and specificity predictable. Avoid accidental dependence
on import order, deep nesting, or repeated overrides. Inspect computed styles,
inheritance, and matched rules to fix conflicts at their source. A shared token or
primitive change needs consumer awareness; a local request should not silently
restyle unrelated features. CSS Modules still permit inheritance and global rules.

## Respect the execution environment

Follow the installed framework's routing and server/client model. Keep privileged
operations and secrets out of client imports and bundles. In SSR applications,
produce deterministic initial markup and investigate hydration mismatches.
Use configured type checks, linting, and formatting; do not hide contract defects
with blanket `any`, unchecked casts, or suppressed lifecycle warnings.

React guidance: [state structure](https://react.dev/learn/choosing-the-state-structure)
and [effects](https://react.dev/learn/you-might-not-need-an-effect).
