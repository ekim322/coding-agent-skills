# HTTP adapters and application behavior

A backend supports HTTP requests and background jobs. Its HTTP surface should be
findable under `api/<capability>/`, but routes currently live inside application
packages and some coordinate the operations themselves.

## Reason through the boundary

There are two decisions: where HTTP code belongs, and what it owns. Moving a route
under `api/` improves placement but does not remove business policy from its body.
Making a handler concise does not establish the intended package boundary either.

The HTTP adapter parses requests, obtains authenticated context, calls an
application operation, and translates outcomes into responses. The application
owns permissions, state transitions, consistency, and recovery regardless of the
caller. Shape validation at the route cannot replace those application checks.
Shared HTTP dependencies belong with the adapter, without importing route modules.

A partial arrangement expressing that decision is:

```text
api/
  auth/                 # Account/session HTTP endpoints
  workspace/
    checkpoints.py      # Checkpoint request/response handling
workspace/
  checkpoints/          # Checkpoint operations and their collaborators
  ...
auth/
  ...                   # Account/session behavior
```

The call is `HTTP handler → checkpoint operation → storage`. A background job can
call the same checkpoint operation without constructing a request or importing
FastAPI. Changing cookie handling should stay in the adapter; changing checkpoint
consistency belongs with the operation.

## Tradeoffs and limits

Centralizing HTTP makes endpoints easier to find, at the cost of visiting both
adapter and application packages for some feature changes. An explicit project
convention may choose feature-local transport instead; preserve separation of
responsibilities there too. This excerpt does not prescribe the application's
internal depth, a service class, or one file per endpoint. Split endpoint groups
and application components by coherent responsibilities as they develop.
