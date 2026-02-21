# Dquode documentation roadmap (v1.0)

## Why we're building this

DQuode was built with absolute simplicity and ease in mind: simple to setup, easy to manage, minimal configuration and scaffolding, and speed.

- **A Python framework that takes seconds to setup** — No heavy scaffolding; get an app running in seconds.
- **A simple, fast, robust ORM with no extra setup** — Built-in MySQL and Mongo support; no wiring SQLAlchemy or drivers. Just plug a `.env` and you're good.
- **Simple error catching and validation** — Structured response from any part of the system (status, message) and easy request validation so developers see what went wrong and fix it fast. No over-reliance on status codes that don't tell the full picture.

---

## Table of contents (doc structure)

1. **Introduction** — Problem statement, proposed solution, key features; philosophy (what is DQuode, problem with modern backend, DQuode approach, core principles).
2. **Setting up** — Requirements, Setup, Start the server, Test the setup.
3. **Architecture** — Architectural overview, project structure, MVC in practice, request lifecycle, query execution flow.
4. **Routing** — Defining routes, route groups, routing guarantees (404, 405, path normalization, cached routes).
5. **Models** — Creating a model, linking models to tables.
6. **Controllers** — Creating controllers, key aspects, defining controller functions, handling request data (body, validation, other), returning responses (standard, custom).
7. **Views** — Creating views, defining routes, multiple routes and route groups, steps to register, additional options.
8. **Database operations** — Setup (env, model), Read (filters, operators, boolean, single/multiple), Create (single, bulk), Delete, Update; filters and params (one page for both backends).
9. **Limitations** — Multiple conditions on same key (list of condition dicts), full structure replacement, server restart; no migrations, DB constraints external.
10. **Guides & reference** — Auth, Validation, Config, CORS, Extensibility, Scheduler, Security, Files; API reference; Changelog, Deployment.

---

## Documentation pages (what goes in each)

### Introduction

- **Overview (Introduction)** — What Dquode is; **Philosophy:** (1) What is DQuode — native-first Python backend, full stack (routing, ORM, validation, auth, response), clear MVC. (2) The problem — complex setup, fragmented ORM, inconsistent responses, scattered validation, heavy deps, decision fatigue, harder maintenance. (3) The DQuode approach — clear MVC, unified response contract, built-in ORM (MySQL + Mongo), integrated validation, centralized config, minimal deps, run-and-go. (4) Core principles — structure over chaos, native-first, minimal deps, unified SysCodes/SysMessages, backend–frontend contract, predictable layout, fast setup. One paragraph "when to use it".

### Setting up

- **Requirements** — Python version, pip, supported DBs (MySQL, Mongo). No Redis.

- **Setup** — Clone or template, `pip install -r requirements.txt`, copy `.env` from `.env.template`. List env vars: ENVIRONMENT, SERVER_PORT, DB_TYPE, DB_HOST, DB_PORT, DB_NAME, DB_USER, DB_PASSWORD, DB_USE_TLS.

- **Start the server** — `python start.py`; development (reload when ENVIRONMENT ≠ live) vs live (ENVIRONMENT=live, reload off). Console: lifespan, DB connected, routes cached.

- **Test the setup** — One request (e.g. health route) and confirm unified response (data, message, status, statuscode).

### Architecture

- **Architectural overview** — ASGI (uvicorn, index:app); lifespan (AppContext.init, AppContext.shutdown); routing (Dispatcher, route list from views, path/method match, handler); MVC (Models = Database subclasses, Controllers = handlers, Views = route definitions); ORM (Database + initDbms from DB_TYPE, same CRUD API); validation (applyRules/trimApplyRules); auth (pluggable handler, request.user()); response (Response + response(), unified envelope, trace by environment); scheduler (Celery, runAsyncJob, beat); security (Encrypt).

- **Project structure** — start.py (entry, uvicorn), index.py (ASGI, lifespan, Request → Dispatcher → Response), context.py (AppContext: redis, db, routes; init/shutdown), registers.py (auth, extendCodes/extendMessages), controllers/, models/, views/ (endpoints.py, routes), dreema/ (requests, responses, routing, orm, security, helpers, scheduler, files). Short note per item.

- **MVC in practice** — Models = data (Database, setTable, CRUD); Controllers = business logic (handlers get request, call model, return response); Views = routing (routes list, route/routegroup, Dispatcher uses it).

- **Request lifecycle** — ASGI → Request(scope, receive, send) → Dispatcher.dispatchRoute() → CORS → route match → handler(request) → controller (body, validation, user, model) → return → Response.response(content) → HTTP.

- **Query execution flow** — Controller calls model.read/create/update/delete → Database.initDbms() (MySQL or Mongo) → query builder → connector executes → (data, status, message) back to controller.

### Routing

- **Defining routes** — route(path, methods, handler) from dreema.routing; path, methods list, handler. Example.

- **Route groups** — routegroup(cls, prefix, postfix). Multiple routes, steps to register (add to routes, import handler).

- **Routing guarantees** — 404 when no path match; 405 when method not in route.method; path normalization (trailing slash, double slash); routes cached at startup (Dispatcher.initRoutes) for fast lookup.

### Models

- **Creating a model** — Subclass Database, set tablename, setTable in __init__. Minimal example.

- **Linking models to tables** — setTable(tablename) sets table/collection name; same API for MySQL and Mongo; no migrations.

### Controllers

- **Creating controllers** — Handlers in controllers/; async def handler(request); imported in views.

- **Key aspects** — Single request arg; use body(), params(), user(), applyRules/trimApplyRules; return response() or Json.

- **Defining controller functions** — Signature, async, one request argument. Example.

- **Handling request data** — Request body: body(), .data. Validation: applyRules/trimApplyRules, rules (required, int, str, float, list, bool, nullable), return (status, data, message). Other: params(), headers(), method(), path(), auth(), user().

- **Returning responses** — Standard: response(message, status, data, statuscode, trace, headers); envelope. Custom: response(..., custom=True). Trace stripped when ENVIRONMENT not debug/local.

### Views

- **Creating views** — views/endpoints.py (or similar), exports routes list.

- **Defining routes** — route(path, methods, handler). Example.

- **Multiple routes and route groups** — routes = list of route() or lists; routegroup(prefix, postfix). Steps to register multiple routes.

- **Additional options** — Path normalization, method check, 404.

### Database operations

- **Setup required** — Env: DB_TYPE, DB_*, etc. Model: setTable(tablename).

- **Read** — read(filters, params). First vs multiple (limit, skip, sort, sortfield). Filters: field → value or {op, value}. Operators (=, !=, >, <, in, nin). Boolean (and/or via params). Params: limit (0 = no limit), skip, sort, sortfield, include/exclude (last wins).

- **Create** — create(data): single (dict) or bulk (list). Return shape (e.g. lastInsertedId/_id, status, message).

- **Delete** — delete(filters, params). One vs many (params). Same filter shape.

- **Update** — update(filters, data, params). One vs many. Same filter shape.

- **ORM — Filters and params (one page)** — Same for MySQL and Mongo: filter shape; list of condition dicts for same field (e.g. range); params: limit, sort, skip, sortfield, include/exclude.

### Limitations

- **Multiple conditions on the same field** — Python dicts can't have duplicate keys; use a list of condition dicts for that field, e.g. name: [ {op: '>', value: 10}, {op: '<', value: 100} ].

- **Full structure replacement** — Update behaviour per engine ($set/SET); document current semantics.

- **Server restart** — In-memory state (route cache, connections) reset; lifespan re-runs.

- **No migrations; DB constraints** — Tables/collections created elsewhere; constraints managed externally.

### Guides & reference

- **Auth** — setAuthHandler(handler) in registers.py; handler signature and return Json. In controllers: await request.user() or request.user(types=[...]). Auth response (data, status, message). **Enforcement:** framework does not enforce 401/403; controller must check auth result and return error response.

- **Validation** — applyRules, trimApplyRules; rule names; return value; example.

- **Config** — .env, getenv; config.CONFIG, getconfig; registers.py (auth, extendCodes, extendMessages).

- **CORS** — getconfig("cors"): allowedOrigins, allowedMethods, notAllowedHeaders, defaults. Checked in Dispatcher before route match.

- **Extensibility** — extendCodes, extendMessages, config. Example.

- **Scheduler** — Celery in dreema.scheduler.setup; broker/backend from env; run worker and beat; runAsyncJob(coroutine), configureBeatSchedule.

- **Security utilities** — Encrypt: hash, verifyHash, generateRandom, getSecret. When to use.

- **Files** — Multipart, FileParser, request.body() for files. Optional.

- **API reference — Routing** — route, routegroup, Dispatcher, initRoutes, dispatchRoute. Params and return types.

- **API reference — Request** — Request and all methods, signatures, return type.

- **API reference — Response** — response(), Response, StatusCodes, SysCodes, SysMessages, extendCodes, extendMessages.

- **API reference — ORM** — Database: connect, setTable, initDbms, create, read, update, delete.

- **API reference — Security** — setAuthHandler, getAuthHandler, authenticate; Encrypt methods.

- **Changelog** — Version, date, notable changes.

- **Deployment** — ENVIRONMENT=live, run app (and Celery if used), env and config.
