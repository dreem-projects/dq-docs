# Key aspects of controllers

## Async only

Controller handlers must be **async** and are awaited by the Dispatcher. Use `await` for I/O (e.g. `request.body()`, model calls).

## Request is the first argument

Every handler receives exactly one argument: the **Request** object. No dependency injection; read body, params, and headers from `request`.

## Auth is your responsibility

The framework does **not** enforce 401/403. If you use an auth handler (e.g. via `registers.setAuthHandler`), the controller must:

- Call the auth logic (e.g. validate token).
- If auth fails, return an error response with the appropriate status/message (e.g. 401 Unauthorized, 403 Forbidden).

So: **you** decide when to return 401/403 based on the auth result.

## Validation is explicit

Use `request.applyRules(...)` or `request.trimApplyRules(...)` to validate body or params. If validation fails, check the returned status and return an error response (e.g. `response(body, custom=True)` to forward the validation result).

## Unified response

Prefer returning via **response(...)** so the client always gets the same envelope: `data`, `message`, `status`, `statuscode`. Use `response(..., custom=True)` when you need to pass through a custom envelope (e.g. validation error object).

## One handler per route

Each route points to one controller function. To support multiple actions (e.g. list vs create), define multiple routes (e.g. `GET /items` and `POST /items`) to different handlers.
