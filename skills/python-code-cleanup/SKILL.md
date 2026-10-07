---
name: python-code-cleanup
description: Clean up existing Python code for readability through meaningful function boundaries, intent-revealing names, and plain-language docstrings and comments while preserving behavior. Use for Python readability refactors or documentation cleanup, not feature development or repository documentation audits.
---

# Python code cleanup

Make code understandable progressively: a reader should first see what the operation accomplishes, then open only the details they need. Keep each function at one coherent level of abstraction. Documentation should explain purpose and usage without requiring familiarity with the package's internal vocabulary.

## Establish the scope

Read the repository's AGENTS.md and relevant architecture and package guides. Inspect the implementation, callers, tests, and current diff before editing. Distinguish existing behavior from intended architecture or prompt guidance.

Honor the requested scope. A documentation-only request permits only docstrings and comments; do not extract helpers or rename code. A general cleanup request permits internal readability refactoring while preserving public interfaces and observable behavior. Do not fold unrelated bugs, new features, dependencies, or architectural redesign into cleanup. Preserve unrelated changes in the checkout.

For a folder-wide request, inspect every file, but change only code or documentation that benefits. When parallel agents are requested, assign exclusive files and provide these standards to each agent; review the combined changes centrally.

## Let the code express the workflow

Keep coordinating methods at the level of the operation they perform. Extract implementation machinery when it hides that flow: pagination, batching, retries, provider limits, parsing, or response conversion can be meaningful units of complexity.

For example, a transcript search can read as:

```python
calendar_results = await self._find_matching_company_events(search_input)
event_ids = self._collect_event_ids(calendar_results)
transcript_results, warnings = await self._find_transcripts_for_events(event_ids)
```

The reader can understand event discovery followed by transcript retrieval without mentally simulating endpoint pagination. The helpers should own those actual responsibilities, not merely hide arbitrary slices of code.

- Extract based on responsibility, not a line-count limit. A long cohesive function may be clearer than several fragments.
- Keep simple, readable operations inline. Do not turn result construction or one obvious expression into a helper merely to make the caller shorter.
- Name helpers for the operation and its subject. Avoid vague names such as `_process`, `_handle_data`, or `_finalize_result`.
- Do not introduce classes, generic frameworks, new modules, or chains of wrappers just to tidy one method. Prefer the smallest structure that makes the work clear.
- Make inputs, outputs, and meaningful side effects evident. Avoid helpers that depend on hidden mutable state or return many unrelated values.
- Keep consequential decisions visible at the appropriate level: partial results, early exits, recovery policy, and authorization should not disappear behind an innocuous name.
- Preserve call ordering, batching, concurrency, resource ownership, cancellation, exceptions, retry behavior, and result ordering. Moving code across a `try`, lock, transaction, or async context boundary can change behavior.

After extraction, read the coordinating method alone. It should explain the workflow. Then read each helper: it should have a useful responsibility that justifies opening another function.

## Make file and folder responsibilities discoverable

For package- or folder-wide readability cleanup, assess module and package names
as well as names inside functions. A reader should be able to locate an operation
from the directory listing and full import path. Scrutinize generic names such as
`service.py`, `provider.py`, `manager.py`, and `utils.py`; prefer the actual subject
and responsibility when that improves discovery. Common usage alone does not
establish clarity. Keep conventional names when their purpose is evident, and
avoid needless repetition of the containing package name.

Establish what a module owns before proposing a rename. For example, a module
listing Drive files could be named `drive_files.py` rather than `provider.py`.
A clearer filename does not require splitting cohesive code or adding layers.
Apply internal renames within the authorized cleanup scope after checking imports,
entry points, and string-based references. Preserve supported public import paths;
report a proposed change separately when it would require a public interface
change or architectural redesign. Documentation-only requests still permit no
renames, and a local function cleanup does not imply a package-wide naming audit.

For package- or folder-wide cleanup, reassess the current grouping as well as its
names. Earlier files may reflect responsibilities that have since grown or
changed. Check for independent concerns accumulating in one owner, related rules
scattered across files, and small fragments whose shared context requires needless
navigation. Clear functions do not by themselves make the overall arrangement
clear. Compare keeping, splitting, and regrouping the affected code.

When structural cleanup is authorized, split or regroup internal modules and use
subpackages where they make a coherent responsibility easier to find and follow.
Neither file size, file count, nor flatness alone justifies a change. A narrow
function or documentation cleanup does not authorize this restructuring; report
concrete broader boundary problems separately. Preserve public imports, resource
and transaction lifetimes, and observable behavior, and verify affected callers.

## Preserve observability and queryability

Treat operational telemetry as observable behavior during cleanup. Read the repository's observability guide when changing instrumented code, and inspect inherited logging and tracing before adding wrappers. Preserve the evidence needed to identify an execution, reconstruct meaningful events, and locate failures or slow stages.

- Keep correlation scopes around the work they describe. Moving task creation, retries, exception handling, or persistence across a scope can lose IDs, change parentage, or misreport duration and outcome. Verify context isolation and restoration; in-process context does not automatically survive queues or restarts.
- Preserve stable event names, structured field names, operation names, severity, metric units, and dimensions used by queries or dashboards. A Python helper rename does not justify renaming the telemetry schema. Keep durable request/run/job/domain IDs and trace/span IDs available for their respective joins.
- Make observability intent visible at call sites. Prefer explicit names such as `bind_observability_context` and `observe_operation` when naming new internal helpers or performing an authorized rename. Respect existing public interfaces. Explain that context binding attaches fields, logs record events, and observed operations record timing/outcomes when that distinction affects usage.
- Preserve truthful handling of errors, cancellation, retries, partial completion, and final persistence failures. Successful admission must remain distinguishable from successful background execution. Do not duplicate completion/error records merely because code was extracted into more functions.
- Keep structured records bounded and useful. Do not replace queryable fields with interpolated prose, expose credentials or raw user/model payloads, or introduce unique IDs as metric labels. Redaction of field names does not sanitize arbitrary message or exception text.

For general readability cleanup, preserve existing instrumentation and report concrete diagnostic gaps separately. When logging improvements are explicitly included, reuse the existing capability and add evidence at meaningful boundaries rather than every helper. Keep vendor configuration and new telemetry infrastructure outside ordinary cleanup scope.

When instrumentation is moved or changed, verify a representative success/failure path retains its IDs, timing scope, and outcome. Follow the documented query path from environment/time/request or domain ID to relevant logs and traces; use existing tests or captured records where sufficient. Distinguish emitted telemetry from retained, retrievable telemetry: OTLP export alone supplies neither storage nor query access. Do not interpret missing records as success or claim live retrieval from a formatter-only test.

## Write documentation a newcomer can understand

Assume the reader knows Python and basic database concepts, but not this project's terminology.

- Lead with the practical purpose: what does this help the caller accomplish?
- Prefer familiar verbs and concrete nouns. Say “adds graph filters” rather than “performs scope insertion,” and “preserves result column names” rather than “provides column-restoration metadata.”
- Explain the underlying action; replacing jargon with longer jargon does not improve clarity.
- Mention callers, inputs, outputs, and the next responsible layer only when they help the reader understand or use the component. Do not inventory collaborators or architectural boundaries.
- Use exact types and technical terms when the reader needs them to act correctly. Names alone are not explanations.
- Match length to conceptual complexity, not line count. A simple function may need one sentence or no docstring; a coordinator, service, session, or entry point may need several paragraphs to explain its role and workflow. Do not compress a complex contract merely to keep the docstring short.
- For complex components, build a coherent explanation: purpose and place in the system, main workflow and responsibility boundaries, then results and essential lifecycle or failure contracts. These are guiding questions, not mandatory headings. The reader should understand the flow without tracing helper calls.
- Include authorization, side effects, lifecycle, failures, and recovery only when they affect correct usage. Preserve useful warnings and constraints.
- Describe implemented behavior, not intended behavior or guarantees inferred from model prompts.
- Keep parser mechanics, internal placeholders, alias generation, and similar implementation rationale in nearby comments when useful, rather than public docstrings.
- Avoid field inventories, boilerplate parameter sections, code narration, framework-first descriptions, and repetition across module, class, and function docs.
- Comment why a surprising choice exists. Leave obvious operations, getters, and helpers alone.
- Prefer a direct statement of responsibilities over defensive contrasts such as “This is not X.”

Replace wording like:

```python
"""Restrict and scope a graph read without executing it or selecting a graph.

The persistence adapter receives a statement with a reserved scope placeholder
and column-restoration metadata.
"""
```

With wording like:

```python
"""Prepare a query to read only from the graph the caller is allowed to access.

Adds graph filters and preserves the query's result column names. The database
layer supplies the allowed graph, checks that the query cannot write, and runs
it with time and result-size limits.
"""
```

Use this as a language example, not a mandatory structure or length. Retain additional constraints when callers need them, such as explicitly requesting historical records or filtering retired ones.

For a complex editing session, a mechanics-only summary such as:

```python
"""Commit once; identical retries reuse IDs, timestamps, payload and result."""
```

may need a fuller explanation like:

```python
"""Turn a proposed knowledge update into a validated, recoverable graph write.

The graph agent uses this session when it is authorized to change stored
knowledge. The session resolves proposed references against existing
records and captured sources, prepares creations, revisions, and
retirements, then submits them together through the supplied save
capability. Graph storage owns the atomic commit.

One session tracks an edit through submission and confirmation. Once a
write is pending, it retains the exact changes so retries cannot
accidentally create a different update. Confirmed saves return a receipt
identifying the affected records.

A failure after submission may mean the write succeeded but confirmation
was lost. Keep the session and retry the original proposal to resolve
that uncertainty before starting a replacement edit.

The caller must bind graph reads and the save capability to the same
authorized graph. The session uses the access supplied by the caller.
"""
```

This illustrates how purpose, handoffs, and recovery can justify several paragraphs; it is not a specification of current repository behavior. Verify each claim against the component being documented, including whether recovery is in-memory or durable. Do not copy these guarantees into components that do not implement them.

Check whether docstrings feed tool descriptions, schemas, or runtime introspection. Preserve operational instructions and required formatting; prose changes on those surfaces can affect runtime behavior.

## Review and verify

Review the final diff against callers and tests. Check for accidental changes to public names, signatures, serialization, exception behavior, or execution order. Do not rename a callable without checking references, including string-based registration and tool exposure.

For documentation-only work, compare executable structure with the starting version when practical and check whitespace. For refactoring, run focused tests covering the moved responsibilities and their caller. Add a regression test only when it protects meaningful behavior; do not add tests that merely mirror helper structure or wording. Use the package's prescribed checks and report unavailable prerequisites honestly.

Perform a separate editorial pass:

- Can someone unfamiliar with this package explain the purpose without opening several files?
- Does the coordinating function show the operation, with machinery below it?
- Does every extracted helper reduce complexity rather than just move it?
- For package-wide cleanup, does the resulting grouping localize related rules and make their owners discoverable, rather than merely producing smaller files?
- Does each documentation sentence explain purpose, enable correct usage, or prevent a real mistake? Remove it otherwise.
- Are claims supported by implementation, and are important contracts still visible?

Complete the requested edits. Finish with a concise summary of the improvements, verification, and limitations. Include a few short before-and-after examples when they help demonstrate a broad cleanup.
