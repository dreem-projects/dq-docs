# Changelog

## v1.0 (initial)

- **Introduction** — DQuode as native-first Python backend; MVC, unified response, built-in ORM ([supported database systems](getting-started/supported-databases.md)), validation, config.
- **Setting up** — Requirements, setup, start server, test setup.
- **Architecture** — Overview, project structure, MVC, request lifecycle, query execution flow.
- **Routing** — route, routegroup, Dispatcher; defining routes, route groups, routing guarantees.
- **Models** — Creating a model, linking to tables; autoCreateColumns, autoReadColumns.
- **Controllers** — Creating controllers, key aspects, handling request data, returning responses; auth and validation in controller.
- **Views** — Creating views, defining routes, multiple routes and route groups, additional options.
- **Database operations** — Setup, read, create, delete, update; filters and params (one page for both backends).
- **Limitations** — Multiple conditions same field, full structure replacement, server restart, no migrations.
- **Guides** — Auth, validation, config, CORS, extensibility, scheduler, security, files.
- **API reference** — Routing, Request, Response, ORM, Security.
- **Deployment** — ENVIRONMENT=live, run app and Celery, env and config.

All content is based on the **dquode** reference implementation.
