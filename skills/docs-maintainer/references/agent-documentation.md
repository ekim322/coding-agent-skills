# Repository memory and execution

Use this reference for structural audits and refreshes. The design follows
[OpenAI's harness engineering article](https://openai.com/index/harness-engineering/)
and [ExecPlan recipe](https://cookbook.openai.com/articles/codex_exec_plans):
small entry instructions, indexed knowledge, versioned task plans and observable
acceptance. This skill adapts those ideas; it does not require an exact directory
layout or imply a formal certification standard.

## Three layers, different maintenance rules

| Layer | Question it answers | Maintenance rule |
| --- | --- | --- |
| Bootstrap | Where should I look, and which constraints apply? | Keep concise; link to owners rather than embedding every rule |
| Repository knowledge | How does the system work, and why? | Describe current contracts, intended behavior and lasting rationale; label their status |
| Task memory | What am I doing, what happened, and what is next? | Preserve progress, discoveries, decisions, evidence and resume state throughout the task and after archival |

A short `AGENTS.md` can route to existing lowercase architecture/product files.
Around 100 lines is a useful example, not a line-count quota. A large package
reference is acceptable when its scope is clear and readers can jump to the
relevant contract. Do not turn every README into another instruction manual.

## Responsibilities and navigation

| Document | Useful contents |
| --- | --- |
| Root README | Purpose, implemented versus planned surface, shortest runnable path and routes by task |
| Root/scoped AGENTS.md | Essential constraints, task-specific reading and when/where to maintain an ExecPlan |
| Architecture | Current components, data flow, dependency direction and code owners; clearly separate proposed design |
| Package README | Scope, caller/dependency boundaries, public interfaces, setup, observation points, failure/recovery and focused verification |
| Product/design docs | User outcomes, behavior, constraints, alternatives and consequential rationale |
| Quality/security/reliability guidance | Known gaps and relevant invariants with evidence or enforcement links; use existing sections where sufficient |
| Roadmap or debt tracker | Work selection, open choices, known weaknesses, priority/status when established, and links to actual plans |
| PLANS.md | When plans are required, required contents, update points, recovery and completion/archive protocol |
| Active plan | One task's goal, repository context, completed/remaining work, discoveries, decisions, commands, evidence and next action |
| Completed plan | Actual outcome and execution history, including limitations and references to follow-up work |

Detailed facts should have one owner with useful inbound links. Some repetition
is warranted in task plans so they remain resumable; avoid copying entire package
manuals. A private chat reference alone is not durable context. Preserve a confirmed
decision's substance, and explicitly identify missing requirements or sources.

Generated schema/inventory docs need an identified source and generation procedure
when one exists, or an honest statement that they are manually maintained snapshots.
Do not label handwritten docs generated or invent a regeneration command.

## Evidence and runnable instructions

Inspect the source that can support each claim:

- Setup/runtime: manifests, lockfiles, entry points, scripts and configuration loaders.
- Behavior/interfaces: implementation, callers, schemas and relevant tests.
- Intent: explicit user decisions and maintained product/design requirements.
- Live behavior: dated observations of the actual provider or service.

A useful recipe names the working directory, prerequisites, command, observation
point, expected result and consequential effects. Distinguish mocks, offline tests,
real database checks and live integrations. Explain partial writes, retry identity,
resource cleanup or reset boundaries where the behavior requires them.

An instruction to run a check is not evidence it was run. Keep actual outputs,
failures, skips and environment limitations in the execution plan. Link conventions
to their enforcing check where one exists; otherwise identify them as guidance.
Do not invent quality scores, ownership assignments or CI guarantees.

## Establish or repair the planning layer

Read [the PLANS.md asset](../assets/PLANS.md) when working on this layer. Adapt it
to existing plan paths and terminology; merge useful existing rules rather than
replacing a working protocol wholesale. Keep the installed protocol self-contained
inside the repository, without requiring this skill or its conversation.

For a full refresh with no convention, use `docs/PLANS.md`,
`docs/exec-plans/active/`, `docs/exec-plans/completed/`, and a plan index at
`docs/exec-plans/README.md`. The index links to the protocol and existing plans,
stating when there are no active or completed plans. Git does not track empty
directories; create them with the first plan or use a minimal tracking file when
needed. Do not add fake plan instances or backfill unseen decisions.

Add a short rule to the agent entry point, for example: for substantial features,
migrations or refactors, read the local planning protocol, create or continue the
relevant plan, and update it at stopping points. Link the index from the normal
documentation route. Keep the roadmap separate and link selected work to its plan.

When an active plan is incomplete, preserve its recorded progress and add the
missing context from evidence. Mark uncertain history as unknown. Completion
means the agreed outcome and required acceptance are satisfied; otherwise retain
active/blocked status with the exact next action or needed input. Do not archive
unfinished work merely to clean the active directory.

## Validate with two walkthroughs

Choose representative tasks for the repository and requested scope. Report these
as documentation walkthroughs unless commands or product behavior were executed.

**Navigation walkthrough:** start at `AGENTS.md` or the root README. Can a reader
find the intended outcome, owning modules/interfaces, constraints, prerequisites,
run path and observable acceptance without guessing? For a full-stack repository,
cover both backend and frontend responsibilities when doing a full refresh.

**Interrupted-task walkthrough:** use a real plan when available and assume the
conversation is gone. Can a reader identify:

1. The bounded goal and current implementation state?
2. Completed, partial, blocked and remaining work, with the next action?
3. Which approaches were rejected and the reasons?
4. Discoveries and evidence that changed the approach?
5. Exact paths, working directories and commands needed to continue?
6. Validation already performed versus acceptance still outstanding?
7. Safe retry/recovery behavior and the condition for completion?

If there is no real task plan, review the protocol against a plausible task and
label this a protocol walkthrough, not proof of a successful task restart. Do not
manufacture historical results. A checklist of headings passes neither walkthrough
unless the information inside supports the next action.

## Completion of a refresh

The requested scope has clear entry points and document ownership; implemented
and proposed behavior are distinguishable; relevant commands and links resolve;
and the planning layer supports durable state where structural work is in scope.
Conflicting cleanup/planning rules have been reconciled. Remaining engineering,
missing-source or unverified-runtime gaps are explicitly reported. Narrow updates
may leave other layers for later, but must say so rather than claim full alignment.
