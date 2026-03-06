# API reference — Routing

Routes are defined with **route** and **routegroup**; the **Dispatcher** caches them at startup and matches each request by path and method.

## route

```python
from dreema.routing import route

route(path: str, methods: list[str], handler: Callable) -> Route
```

- **path** — URL path, must start with `/`. May include path parameters using `:name` (e.g. `"/items/:id"`). The Dispatcher matches by segment; captured values are available on the request via `request.pathParam()`.
- **methods** — List of HTTP methods, e.g. `["GET", "POST"]`.
- **handler** — Async function with signature `(request: Request) -> Response` (or return value that the framework converts to a response).

Returns a route object used in the **routes** list passed to **initRoutes**.

---

## routegroup

```python
from dreema.routing import routegroup

routegroup(prefix: str, postfix: str)(routes: list) -> list
```

- **prefix** — Prepended to each route path.
- **postfix** — Appended to each route path.
- **routes** — List of route objects (from **route(...)**).

Returns a new list of routes with paths transformed to `prefix + path + postfix`.

---

## Dispatcher

- **initRoutes(routes)** — Normalizes and caches the route list. Call once at startup (e.g. in AppContext.init). **routes** is a list of route objects.
- **dispatchRoute(request)** — Finds a matching route by path and method, invokes the handler with **request**, and returns the response. Handles CORS and returns 404/405 when no match or wrong method.
