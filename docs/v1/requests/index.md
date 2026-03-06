# Requests

The **Request** is the single object that represents an incoming HTTP request. Controllers receive it as the first argument of every handler and use it to read input, validate data, and run auth.

!!! info "Where requests come from"
    The framework creates a **Request** from the ASGI scope and body for each incoming call. The dispatcher passes it into the controller method bound to the route (e.g. `SampleController.sampleCreate(request)`). There is no separate "Requests" folder in your project — the request is provided by the framework; this section documents how to **use** it.

---

## What goes in here (conceptually)

This section covers everything that belongs to **incoming data and request handling**:

| Topic | Description |
|-------|-------------|
| **Request object** | The single argument to controller handlers: body, query, headers, method, path. |
| **Request functions** | The methods you call on the request: `body()`, `queryParam()`, `pathParam()`, `applyRules()`, `trimApplyRules()`, `method()`, `path()`, `headers()`, `user()`, and helpers like `client()`, `server()`. |

You do **not** create Request objects yourself. You **use** them inside controllers to:

- Read the **body** (JSON or form), **query params**, and **path params**.
- **Validate** input with `applyRules` or `trimApplyRules`.
- Inspect **method**, **path**, **headers** (e.g. for auth or routing logic).
- Call **user()** to run the registered auth handler and get the current user or auth result.

---

## Flow

1. Client sends an HTTP request (e.g. `POST /items` with a JSON body).
2. The framework builds a **Request** from the ASGI scope and stream.
3. The dispatcher invokes the handler registered for that route and passes the **Request** as the first argument.
4. Your controller uses **request functions** (see [Request functions](request-functions.md)) to read and validate input, then returns a response.

Next: [Request functions](request-functions.md) — body, query, validation, method, path, headers, auth.
