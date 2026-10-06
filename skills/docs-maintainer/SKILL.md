---
name: docs-maintainer
description: >-
  Audit and repair repository documentation for agentic coding: concise navigation,
  maintained repository knowledge, and versioned execution plans that support
  stateless handoff. Use for documentation refreshes, agent-readiness reviews,
  ExecPlan setup, or targeted README updates. Audit requests are read-only;
  refresh requests include documentation fixes, not application implementation.
---

# Documentation Maintainer

Make the repository sufficient for a contributor with no prior conversation to
find relevant knowledge, execute a change, and resume interrupted work. Evaluate
three separate layers: **bootstrap/navigation**, **repository knowledge**, and
**task memory**. Accurate READMEs alone do not satisfy all three.

For a structural audit or refresh, read
[agent documentation](references/agent-documentation.md). When establishing or
repairing execution planning, use the self-contained
[PLANS.md protocol](assets/PLANS.md), adapting its locations to the repository.
Follow the user's supplied standards when they differ from existing conventions.
The OpenAI-inspired pattern is the target; its example filenames are not a mandate.

## Scope and expected result

- An **audit** reports supported findings and missing layers without editing.
- A **full refresh** repairs content and structure, including missing planning
  guidance, navigation and plan lifecycle. Do not stop at recommendations for
  documentation changes already authorized by the request.
- A **targeted update** stays within the named files or topic. For a README-only
  request, improve those READMEs and identify remaining structural work without
  modifying other files or pretending the planning system already exists.

Keep application code, dependencies, deployment, generated/vendor files and other
skills outside a documentation refresh. Preserve unrelated work and project
constraints. Updating agent navigation and documentation/planning instructions
is in scope; changing engineering policies or adding approval gates is not.
Do not create CI jobs, scheduled agents or external messages merely because the
reference architecture mentions them.

## Establish what exists

Read repository instructions, git status, the root README and documentation
index. Inventory relevant Markdown, scoped instructions, plans and their inbound
links. Inspect manifests, entry points, owners and tests for claims being changed.
Use diffs to prioritize, not to exclude unchanged or missing documentation.

Distinguish implemented, demo, optional, proposed and historical behavior. Code
establishes implementation; it does not cancel a product requirement. A mocked
test does not establish live-provider behavior. Preserve dated evidence and state
which areas remain unreviewed. Do not invent requirements from unavailable chats.

Before editing, map the three layers and their gaps. For a substantial structural
refresh, create or update an execution plan for the refresh itself once the local
protocol is established. Record only work and evidence actually observed.

## Repair the three layers

**Navigation:** keep `AGENTS.md` a concise map with essential constraints and a
route to the planning protocol. Root and package READMEs should expose purpose,
current status, owners and relevant verification without duplicating entire
contracts. Use existing filenames when they already have clear responsibilities.

**Repository knowledge:** give product intent, current architecture, design
rationale, security/reliability constraints and known quality gaps discoverable
homes. These may be sections of existing guides. Separate current behavior from
proposals and keep detailed contracts with their owners. Add a document only when
it has real content or a protocol to own; do not reproduce an empty example tree.

**Task memory:** treat a roadmap and an execution plan as different artifacts.
A roadmap selects work; an ExecPlan preserves the state of one bounded task.
For a full refresh, if this layer is missing:

1. Establish a repository-local `PLANS.md` using the linked protocol, or repair the
   existing equivalent. Prefer `docs/PLANS.md` when no convention exists.
2. Add concise agent/index routing that requires a living plan for substantial
   features, migrations or refactors and explains how to find current work.
3. Establish active/completed locations and a small plan index. Track empty
   locations only as needed; never fabricate task histories to populate them.
4. Reconcile documentation rules that discard task progress or depend on private
   conversation context. Preserve existing plans, rejected alternatives and
   validation evidence; mark uncertainty rather than reconstructing events.

For existing plans, retain completed checkboxes, discoveries, decisions and
actual results. Update current progress and the next action from evidence.
Archive completed plans with their outcomes and validation; promote lasting
contracts into reference docs as well. **Do not apply reference-doc cleanup rules
to execution history.** Never silently mark unfinished acceptance work complete.

## Verify the result

Run the reference guide's navigation and interrupted-task walkthroughs. A reader
must be able to locate the owner and acceptance path, and resume planned work
without the old conversation. Headings and valid links alone do not establish this.

Check local links, anchors, inbound references after moves, commands against
manifests/scripts, and examples against actual signatures and prerequisites.
Use existing documentation checks where available. Run relevant runtime checks
only when necessary to substantiate changed instructions; inspect side effects
before executing reset commands or live provider calls. Report unrun checks.

Review the diff for invented behavior, lost rationale or task history, duplicated
contracts, unrequested policy changes and false completion claims. A focused
mechanical check may be recommended for a demonstrated recurring gap; distinguish
that recommendation from enforcement that actually exists.

## Finish

Report what changed in each relevant layer, the checks actually performed and
remaining gaps. Link the entry point and any real plan/protocol created. Distinguish
source inspection, documentation walkthroughs, local tests and live verification.
Do not claim full alignment after fixing only READMEs, and do not create a separate
audit report unless it is requested or needed as a task artifact.
