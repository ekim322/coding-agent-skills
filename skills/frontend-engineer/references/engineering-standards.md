# Engineering Standards

## Structure follows responsibility

Prefer grouping implementation by capability, while making screen entry points
explicit. Choose ownership before choosing directories:

| Responsibility | Owns | Placement guidance |
| --- | --- | --- |
| Application shell | Navigation, route selection, shared layout, session gates and application-wide lifetimes | An application boundary such as `app/`, or the established framework entry points |
| Page | Composition of one destination and screen-specific coordination | Inside its owning feature when that feature owns the screen; a page/route layer when several independent features are composed or the framework requires it |
| Feature | A coherent capability's UI, state, data contracts, requests and rules | Together under a meaningful capability name, with subpackages as responsibilities emerge |
| Shared UI | Reusable visual and interaction contracts | The existing UI/design-system boundary, with no feature policy |
| Shared transport | Common HTTP/session/error mechanics | A transport boundary; feature schemas and rules remain with their feature |

A page can use multiple features, and a feature can serve multiple pages. Folder
containment need not mirror the rendered component tree. Do not organize reusable
capabilities beneath whichever page first happened to use them.

For a screen clearly owned by one capability, keep its page entry point alongside
that capability. For example, `accounts/AuthenticationPage.tsx` can own a shared
sign-in/sign-up screen, and `accounts/AccountSettingsPage.tsx` can use the same
Google identity component. Separate sign-in and sign-up implementations when their
workflows actually diverge; a mode switch in one shared form does not require two
folders. An `accounts/` umbrella is useful only while its responsibilities remain
coherent and its screen names remain discoverable.

For a screen composing independent capabilities, place composition in the
application's page/route layer. For example, a briefing page can arrange watchlist
updates, document findings and recent chats, while those capabilities retain their
own behavior and data contracts. Compose cross-feature workflows at the appropriate
page or application boundary rather than making one feature own unrelated policy.

Framework routing conventions take precedence over arbitrary file naming. Keep
framework route files focused on their routing/composition responsibilities and
keep reusable capability implementation with its owner. A required route entry
may delegate to a feature screen; do not add an extra page wrapper that merely
forwards props when an existing component already supplies the entry point.

For authored full-screen components, prefer explicit names such as
`AuthenticationPage` and `DocumentsPage` when they clarify the role. Keep a
framework's established `page.tsx` or equivalent convention when it already
identifies that role. Components such as `SignInForm`, `DocumentLibrary` and
`GoogleSignInButton` should advertise their actual scope rather than claiming to
be a page. Apply naming corrections within the authorized scope; this guidance
does not require renaming unaffected screens during a local fix.

Check dependency direction: application/page composition consumes feature
interfaces; reusable features do not import their consuming pages or shell.
Features can use another capability through a deliberate contract when the
collaboration has a coherent purpose. Shared UI and transport do not import
feature rules. Do not require an `index.ts`, facade or adapter for every file;
introduce an interface where it reduces what consumers need to know.

Before settling the structure, trace a realistic change through it: add a second
screen using an existing form, show document selection inside chat, or add a
preview format. Identify which owner changes and which caller composes it. Check
both whether a maintainer can find a screen by its user-facing purpose and whether
related behavior can be changed without searching unrelated folders. Use this
exercise to challenge both excessive nesting and catch-all feature packages.

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

## Make workflows and documentation readable

Let a coordinating component or hook expose the operation's meaningful steps.
Extract request sequencing, stream synchronization, parsing or resource cleanup
when it hides that flow. Keep simple expressions inline. Helpers should name a
real responsibility and reduce reasoning, rather than relocate arbitrary lines.
Use explicit branches or named intermediate values when nested conditionals
obscure a decision. Preserve request order, cancellation, retries, cache
invalidation, event application and resource/focus lifetimes during extraction.

Document substantial page, feature and hook contracts in plain language. Lead
with what the caller or user can accomplish, then explain handoffs and lifecycle
or failure behavior needed for correct use. Mention inputs, outputs and side
effects when they clarify the contract; do not inventory props or collaborators.
For example, an upload hook may need to explain sequential acceptance, retained
partial success and what cancellation can still stop. Verify such claims against
implementation rather than copying intended guarantees from a design document.

Use nearby comments to explain surprising choices such as a stable retry ID,
disabled snapshot refresh during streaming, or an intentional focus fallback.
Avoid narrating obvious JSX, adding boilerplate JSDoc to every component, or
repeating the same explanation across the page, hook and helper. After editing,
read the coordinator alone, then each extracted owner: the flow and documentation
should be understandable without reconstructing the whole feature.

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
