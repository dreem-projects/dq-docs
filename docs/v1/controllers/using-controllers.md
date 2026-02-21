# How to use controllers

This guide explains how to implement business logic, input validation, and custom responses inside your controller handlers. You'll see typical request-processing steps and best practices for organizing controller code before connecting it all to your routes.

!!! tip "One place for the rules"
Validation, authorization, and “who can do what” belong in the controller. The framework gives you the **Request**, [request functions](../requests/request-functions.md), [database functions](../database/database-functions.md) and everything you need to write implement your logic.

---

## Business logic flow

A typical handler follows this pattern:

1. **Optional: Read input** — Body, query params, or both.
2. **Optional: Validate** — Reject bad or missing data; return a clear error.
3. **Optional: authorize** — Check permissions (e.g. via `request.user()`) and return 401/403 if not allowed.
4. **Write your logic** — Call models, run calculations, or enforce invariants
5. **Return a response** — Return your response in [any of the response formats](../controllers/key-aspects.md#shape-your-response)

The controller does **not** define routes; it defines **what happens** when a route is hit. Wire handlers to routes in [Connecting to view](connecting-to-view.md).

---

## Common controller patterns

Here are some full-code examples of these standard controller operations:

- [Create only when valid](implementing-common-intents.md#create-only-when-the-payload-is-valid)
- [Read one or many records](implementing-common-intents.md#read-one-vs-many)
- [Update when valid](implementing-common-intents.md#update-only-when-the-payload-is-valid)
- [Delete with required ID](implementing-common-intents.md#delete-with-a-required-identifier)

---

## Where the rest lives

- **Request and validation:** [Request functions](../requests/request-functions.md), [Handling request data](handling-request-data.md), [Validation](../guides/validation.md).
- **Database:** [Database functions](../database/database-functions.md), [Filters and params](../database/filters-and-params.md).
- **Response shape and statuscode:** [Handling function responses](handling-function-responses.md), [Response functions](../response/response-functions.md).
- **Wiring handlers to URLs:** [Connecting to view](connecting-to-view.md).
- **Async, one handler per route, auth responsibility:** [Key aspects](key-aspects.md).

Controllers stay focused on **business logic**; the framework handles transport, envelope, and dispatch.
