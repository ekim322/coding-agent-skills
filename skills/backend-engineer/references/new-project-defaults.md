# New project defaults

Read this when creating a project, establishing its initial tooling and structure,
or performing requested modernization. These are preferred defaults, not mandates
to rebuild an existing application.

## Selection rule

Default to current, widely adopted, maintained practices rather than familiar
legacy scaffolding or the newest experimental tool. Respect explicit user choices
and established project conventions. Verify current authoritative documentation
when selecting tooling or using version-sensitive APIs; do not hardcode a claim
that a particular tool or version will always be the latest standard.

Apply the defaults without asking the user to choose every routine detail. Record
meaningful deviations and their reasons in the project's owning documentation.
Modernization applies only to the requested scope. Additional services,
deployment, and live provider access require their own task justification and
authorization where applicable.

## Python layout and dependencies

- Put importable application code under `src/<package>/`, or
  `backend/src/<package>/` in a full-stack repository. Keep tests outside `src`.
  Use an installed package and normal imports; avoid relying on manual
  `PYTHONPATH` changes to make startup or tests work.
- Use `pyproject.toml` for Python project metadata, dependencies, development
  dependency groups, and supported tool configuration. Choose its location to
  match the Python project's ownership; a backend subproject can own its file.
  Avoid maintaining duplicate dependency declarations in requirements files.
- Prefer `uv` for a new Python project's environment and dependency workflow,
  with a committed `uv.lock`. Declare a supported Python version and use locked
  installs in CI. Another maintained workflow is appropriate when the project
  or user requires it. Direct dependency pins alone do not lock transitive ones.
- Ensure required resources, such as migration SQL, are available in the packaged
  application. Verify the distribution when deployment depends on it.

## Developer commands and configuration

Provide short, documented commands for setup, running the application, checking
it, and applying formatting. A Makefile is a reasonable default when supported
by the target environment; use an equivalent runner when that fits better.

The run command starts all locally required processes, including workers when
present. Handle readiness, useful startup errors, sibling failures, and clean
shutdown of owned processes. Avoid requiring users to discover individual module
invocations or manage multiple terminals for routine use. Setup should be safe to
repeat and preserve existing configuration and data.

Declare runtime prerequisites, including Node when the project includes a
frontend. Provide a safe `.env.example`, validated settings, and actionable errors
for missing configuration. Keep secrets out of committed examples and output.

## Checks and application conventions

- Configure Ruff linting and formatting, one maintained Python type checker
  such as Pyright or mypy, and pytest for meaningful behavior tests. Run the same
  checks through the local check command and CI. Coordinate frontend checks with
  the frontend's owning workflow when present.
- Keep HTTP handling, application operations, persistence, and external provider
  access understandable as responsibilities. Let actual capabilities determine
  modules; do not generate empty architectural layers from a template.
- Validate request and response contracts, define consistent errors, and keep
  frontend types aligned with the API where applicable. Use current documented
  framework lifecycle APIs for startup and shutdown.
- For persistent databases, provide versioned migrations and test both fresh
  initialization and relevant upgrades. Explain migrations as changes to an
  existing database's structure. Integrate local initialization into setup;
  document consequential upgrades separately and do not implicitly reset data.

## Optional containers and services

Consider Docker Compose when it simplifies a multi-service application or makes
its environment reproducible. It is optional, not a requirement for every Python
backend. If chosen, provide a simple run command, development reload where useful,
health checks for readiness dependencies, persistent storage, and graceful
shutdown. Make stop and destructive reset behavior clearly different.

Choose databases, queues, and other services for actual requirements. Do not add
Redis, Kubernetes, a new database, or telemetry vendors just to complete a generic
project template.

## Acceptance

Verify the documented workflow in a clean temporary environment, using synthetic
configuration and data where possible. Establish that dependency installation,
database initialization, startup, checks, and shutdown work without relying on
undeclared tools or the developer's existing environment. Check that repeated
setup preserves data. Distinguish observed results from untested live-provider
behavior, and report any unavailable prerequisite that limits verification.

## Authoritative references

Consult the relevant current documentation when applying these defaults:

- [Python project configuration](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
- [Python src and flat layouts](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/)
- [uv locking and syncing](https://docs.astral.sh/uv/concepts/projects/sync/)
- [Ruff](https://docs.astral.sh/ruff/)
- [FastAPI lifespan](https://fastapi.tiangolo.com/advanced/events/)
- [Compose startup and readiness](https://docs.docker.com/compose/how-tos/startup-order/)
