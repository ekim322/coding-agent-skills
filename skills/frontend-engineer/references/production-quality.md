# Production Quality

A UI must remain understandable when content, input, timing, or available space
changes. Apply the relevant guidance to the actual journey; do not add a new
design system, telemetry stack, or state framework just to satisfy this reference.

## Design the complete interaction

Use clear hierarchy, readable typography, consistent spacing, and deliberate
primary actions within the product's visual language. Exercise realistic content:
long text, missing data, dense tables, and large values. Adapt wrapping, overflow,
scrolling, and overlays to supported viewports, zoom, and input methods. Reserve
space for delayed content and keep animation purposeful with reduced-motion support.

Make relevant loading, empty, partial, stale, denied, and error states distinct.
A failed request is not an empty result. Keep useful content during refresh when
appropriate and give actions immediate feedback. Preserve recoverable drafts after
failure and provide an actionable recovery path. Treat destructive actions with
proportionate confirmation or undo.

Account for rapid interaction, navigation, refresh, and session expiry. Prevent
accidental repeated submissions and coordinate retry safety with the backend;
disabling a button alone does not establish idempotency. Sorting, filtering, and
pagination must not imply coverage of records that were never fetched.

For streamed output, distinguish partial and final results, surface failure or
cancellation, and preserve the user's scroll position when reading earlier content.
Bound accumulated output and rendering frequency.

## Accessibility is part of the contract

Use semantic HTML and native controls first, with persistent input labels and
accessible names. Reuse accessible primitives for complex widgets. Support keyboard
operation, visible focus, appropriate tab order, and focus restoration after dialogs
or navigation. Do not leave users trapped after an overlay closes.

Associate errors and help with fields. Announce important asynchronous changes
without announcing every render or streamed token. Meet the project's contrast
and target-size requirements; do not rely only on color, hover, or motion.
Check reflow, zoom, reduced motion, and assistive-technology behavior when affected.
Automated scans do not establish that a journey is usable.

Reference: [W3C accessible forms](https://www.w3.org/WAI/tutorials/forms/).

## Performance and resources need evidence

Measure before optimizing, using realistic content and relevant devices/network
conditions. Inspect load time, responsiveness, layout stability, request counts,
and expensive work. Profile rerenders and long tasks before adding memoization.
Async functions do not move CPU work off the main thread.

Avoid unnecessary waterfalls and duplicate requests using existing data tools.
Keep bundles/assets proportionate, split or defer work where useful, and optimize
image/font delivery. Bound lists, caches, history, polling, prefetching, retries,
and stream buffers. Paginate or virtualize substantial data when justified while
preserving keyboard operation and access to information.

Clean up background work and pause unnecessary activity. Use workers for measured
CPU bottlenecks. Report workload and before/after evidence for performance claims;
local measurements are not real-user telemetry.

## Keep trust on the correct side of the boundary

The server must authorize every action/resource. Client route guards and hidden
controls only shape presentation. Follow established session and CSRF mechanisms
and handle server denials without exposing sensitive data.

Treat API results, user input, files, URLs, and model output as untrusted. Avoid raw
HTML injection; sanitize rich HTML with established tooling and constrain link
protocols. Never evaluate received content as code. Keep credentials out of bundles,
URLs, logs, screenshots, and browser persistence. Minimize sensitive telemetry and
clear or isolate user-specific caches and state when identity changes.

## Make behavior inspectable

Use documented startup commands, known routes, and non-production fixtures. With
authorized tools, inspect console errors, failed requests, DOM/accessibility state,
and visual output. Tie evidence to the app instance and relevant interaction;
avoid interference with other running instances or user data.

Keep diagnostic capture scoped and redacted. Explain unexpected console errors,
failed requests, or hydration warnings instead of suppressing them. If required
access is unavailable, report the exact verification gap; do not invent credentials
or expand the task into an observability migration.
