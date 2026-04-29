# Changelog

## v1.0 (initial)

- **Introduction** — Dreema as native-first Python backend; MVC, unified response, built-in ORM ([supported database systems](getting-started/supported-databases.md)), validation, config.
- **Setting up** — Requirements, setup, start server with `python start.py` (CLI: `--port`, `--host`, `--reload`/`--no-reload`, `--workers`, `--log-level`; port auto-retry if in use), test setup.
- **Architecture** — Overview, project structure, MVC, request lifecycle, query execution flow.
- **Routing** — route, routegroup, Dispatcher; defining routes and route groups.
- **Models** — Creating a model, linking to tables and changing databases.
- **Views** — Creating views, defining routes, multiple routes and route groups, additional options.
- **Database operations** — Setup, read, create, delete, update; filters and params (one page for both backends).
- **Limitations** — Multiple conditions same field, full structure replacement, server restart, no migrations.
- **Guides** — Auth, validation, config, CORS, extensibility, scheduler, security, files.
- **API reference** — Routing, Request, Response, ORM, Security.
- **Deployment** — ENVIRONMENT=live

All content is based on the **Dreema** reference implementation.
