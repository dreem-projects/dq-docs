# Additional options for views

Beyond single routes and route groups, you can organize and scale your routing in a few simple ways.

## Splitting by domain

- **views/endpoints.py** — Public API (e.g. `/`, `/sample-read`).
- **views/admin.py** — Admin-only routes under a prefix (e.g. `routegroup(prefix="/admin", ...)`).
- **views/api_v1.py** — Versioned API (e.g. `prefix="/api/v1"`).

Merge all lists in `context.py` and pass to `Dispatcher.initRoutes()`.

## Consistent handler style

- Prefer one controller module per “resource” or area (e.g. `sampleController`, `userController`).
- In views, import the controller module and reference class methods or functions: `handler=Sample.SampleController.welcome`. This keeps the route list readable and avoids circular imports if controllers do not import views.

## No view-level middleware

In this version, DQuode does not attach middleware at the view level. CORS and global behavior are handled in the Dispatcher. Per-route logic (auth, validation) lives in the **controller**.
