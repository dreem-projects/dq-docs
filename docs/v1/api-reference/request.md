# API reference — Request

The **Request** object wraps the ASGI scope and body. Controllers receive it as the first argument.

## Methods (summary)

- **body()** — Async. Returns parsed body (e.g. JSON/form). Result has **data**, and possibly **status**, **message**.
- **queryParam()** — Returns query parameters from routes. (e.g. `/{"id"}:"42"` for a route like `/item?id=42`).
- **pathParam()** — Returns path parameters captured from the route (e.g. `{"id": "42"}` for a route like `/items/:id`).
- **applyRules(rules [, source])** — Validates **rules** against **source** (default body). Returns result with **status**, **data**, **message**.
- **trimApplyRules(rules [, source])** — Like applyRules but trims **source** to only keys in **rules**. **source** can be body or **queryParam()**.
- **user([types])** — Calls the registered auth handler; returns auth result (e.g. user data, status, message). Optional **types** may filter auth type.
- **method()** — HTTP method (e.g. `"GET"`, `"POST"`).
- **path()** — Request path.
- **headers()** — Request headers (implementation-dependent).

Exact signatures and return types are defined in **dreema.requests**. Use these in controllers to read input, validate, and check auth.
