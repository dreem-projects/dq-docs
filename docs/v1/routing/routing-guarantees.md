# Routing guarantees

The Dispatcher provides these behaviors so your routes behave predictably.

## Startup caching

- Routes are **read once** at startup when `Dispatcher.initRoutes(routes)` runs (e.g. inside `AppContext.init()`).
- Changing the code that builds the `routes` list requires a **server restart** (or reload in development) to take effect; there is no hot-reload of route definitions only. Leverage the hot-reload capability [on server startup.](../getting-started/optional-parameters.md)

## Single match

- Each request is matched against the **cached route table** (built at startup from `initRoutes()`).
- **One route** is chosen by **path + method**: the path is normalized, then the first matching route for that method is used. **Static** paths (no `:param` segments) are matched first; **dynamic** routes (with path parameters) are tried only when no static route matches.
- No automatic “fallback” route; if no route matches, the framework returns **404** (path not found).

## Path normalization

- Paths are normalized when routes are cached (e.g. consistent handling of trailing slashes and case, depending on implementation).

## Method strictness

- Only the **methods** you list for a route are accepted. A `GET` request to a route that only has `["POST"]` results in **405 Method Not Allowed**.
- Listing multiple methods (e.g. `["GET", "POST"]`) allows both on the same path.

## CORS

- If CORS is configured, the Dispatcher handles **preflight** (OPTIONS) and adds CORS headers to responses. Actual route matching for GET/POST/etc. is unchanged; CORS does not override method or path matching.

## Summary

- One match per request (path + method).
- 404 when no route or wrong method.
- Paths normalized at init; route list fixed until restart/reload.
