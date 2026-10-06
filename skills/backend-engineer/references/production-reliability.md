# Production Reliability

Reason about what happens when dependencies slow down, operations overlap, or the
process stops. Apply the relevant guidance to the actual operation; reuse existing
mechanisms and avoid adding a reliability framework without a concrete need.

## Remote work has a budget and uncertain outcomes

- Give the complete operation a deadline, including queueing, pagination, retries,
  and backoff. Configure client timeouts and reuse lifecycle-managed connections.
- Assign retry policy to a clear owner so SDK retries, application retries, and
  job redelivery do not multiply silently. Retry identified transient failures
  only when repetition is safe, with bounded attempts/time, jittered backoff,
  and `Retry-After` respected within the remaining budget.
- A timeout does not prove a mutation failed. Use supported idempotency or
  reconciliation before repeating an operation with ambiguous side effects.
- Prefer bounded bulk operations over N+1 calls. Preserve input/result mapping
  and expose partial failures or truncation. Add caching, coalescing, or circuit
  breakers only for a demonstrated need, with explicit scope and recovery.

## Capacity and task ownership are explicit

- Bound growing work: input, fan-out, queues, pools, pages, result bytes, caches,
  and retries. Streaming still needs time and byte limits. Enforce backpressure
  or rejection rather than moving unbounded growth into waiting requests.
- Bound task creation as well as active I/O. A semaphore inside thousands of
  eagerly created tasks does not bound task memory. Account for aggregate load
  across requests and replicas; concurrency limits are not rate quotas.
- Keep blocking I/O and substantial CPU work off the event loop, using bounded
  execution capacity. Offloading does not make resources unlimited.
- Give tasks an owner, failure path, cancellation policy, and shutdown behavior.
  Prefer structured concurrency with deliberate sibling-failure semantics.
  Propagate cancellation and clean up resources; cancelling an await may not stop
  a thread or remote mutation. Do not silently detach required work.

## Durable state must survive races and interruption

- Enforce data invariants with appropriate database constraints. Use atomic
  statements, version checks, or deliberate locking/isolation for competing
  writers; a transaction alone does not make check-then-write safe.
- Keep transaction ownership explicit and transactions short. Avoid holding locks
  across external calls or sharing mutable database sessions concurrently.
  Coordinate external side effects with recoverable state transitions.
- Paginate scans with stable ordering, batch within limits, and avoid N+1 queries.
  Match indexes to actual access patterns and budget connections across workers.
- Plan migrations for old/new application compatibility, lock duration, bounded
  backfills, and recovery. Do not rewrite already-applied migrations silently.
- For duplicate delivery, define idempotency scope, payload matching, atomic
  claiming, concurrent attempts, stored outcomes, and retention. A key reused
  with a different payload must not replay success.
- Required work must survive process loss through durable execution. Define job
  acceptance, acknowledgment/commit ordering, attempt limits, and abandoned-work
  recovery. In-process background tasks do not supply durability. When database
  changes and event publication must agree, use an outbox or equivalent recovery
  mechanism; queue settings alone do not establish exactly-once execution.

## Trust is enforced at the operation

- Derive identity from trusted context and authorize the action, resource, and
  tenant, including bulk operations and jobs. Include permissions and tenant
  scope in relevant cache keys. Submitted identifiers are not authorization.
- Parameterize queries and allowlist dynamic identifiers. Constrain paths,
  uploads, decompressed sizes, and URL fetching; SSRF controls must account for
  redirects and private destinations.
- Use established authentication and cryptographic libraries, verify webhook
  authenticity where relevant, and preserve TLS verification. Keep credentials
  out of code, logs, and responses and use least-privilege access.
- Treat external documents, tool results, and model output as untrusted data.
  Validate results and enforce permitted actions in code before side effects.

## Failures must be diagnosable

Design evidence around questions an operator or coding agent will ask: which
action failed, what happened before it, and which stage consumed the time?

- Use structured logs for meaningful events, state transitions, retries, and
  failures. Prefer stable event names and queryable fields to prose parsing.
  Include relevant counts, outcomes, and safe exception types/code locations.
  Avoid logging every helper, successful poll, or repeated copy of one failure;
  reuse boundary completion records when they already provide the evidence.
- Use spans for meaningful execution stages and dependency calls. Reuse existing
  automatic instrumentation before adding manual spans. An outer span supplies
  total duration, not a breakdown of uninstrumented inner work. Distinguish
  queue wait, execution, and stream lifetime when interpreting latency.
- Use metrics for rates, latency distributions, retries, and saturation where
  relevant. Keep dimensions bounded: operation, outcome, route template. Put
  request, conversation, run, job, and user identifiers in logs/spans, never
  metric labels. Choose units and histogram buckets suitable for the durations
  being measured; SDK defaults may conceal meaningful latency differences.
- Bind correlation fields where identities become known, then inherit them
  through calls and async work. Binding context alone emits no evidence. Verify
  task isolation and restore context on scope exit. Trace/span IDs describe
  execution relationships; durable domain IDs join separate requests and retries.
  Across queues/process restarts, persist those IDs and deliberately propagate
  trace context or links when supported; do not assume in-process context survives.
- Record handled failures, cancellation, partial completion, and final persistence
  failures accurately. Admission success is not background execution success.
  Do not report a timeout as proof a remote mutation never happened.
- Keep fields and payload sizes bounded. Omit credentials, raw prompts/results,
  request bodies, and sensitive URLs. Field-name redaction does not sanitize
  arbitrary interpolated messages or exception text. Use the repository's safe
  exception handling and data policy.

Keep instrumentation independent of the storage vendor. OTLP transports telemetry;
it does not provide retention or a query API. Reuse lifecycle-owned configuration
and bounded export, and keep optional telemetry failures from breaking application
work. Required audit records have separate durability requirements.

Verify queryability for changed boundaries: from an environment, timezone-aware
time window, and request/domain ID, retrieve the relevant events, pivot to the
execution trace, and identify outcome and timing. For broader incidents, start
with metrics and drill into representative traces/logs. Reuse existing CLI or
backend query interfaces; return bounded results and expose truncation. Document
missing retention, sampling, instrumentation, or read access instead of treating
no matches as success. Export credentials do not imply query authorization.

Use focused checks for correlation, truthful outcomes, and emitted fields when
instrumentation changes. When claiming end-to-end export/query support, verify
retrieval from the configured test destination, not just logger calls or exporter
construction. State whether evidence came from fakes, local runtime, or deployment;
do not contact production or incur provider work merely to validate an unrelated
change. Measure before optimizing and retain before/after evidence.
