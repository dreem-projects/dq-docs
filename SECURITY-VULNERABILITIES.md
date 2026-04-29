# Dreema Framework — Vulnerability Assessment

This document lists identified security issues in the **Dreema** framework (codebase under `Dreema/`). Each item includes location, risk, and recommended fix.

---

## Critical

### 1. CORS: Origin reflection + credentials true (no allowlist in response)

**Where:** `dreema/responses/response.py` (defaultHeaders), `dreema/routing/cors.py`

**Issue:** Every response sends:

- `Access-Control-Allow-Origin: <request Origin header>` (origin is reflected, not validated against config)
- `Access-Control-Allow-Credentials: true`

CORS processing only blocks the request when origin is not in `allowedOrigins`. When it is allowed (e.g. default `["*"]`), the response still reflects whatever `Origin` the client sent and always sends credentials. So any site can send `Origin: https://evil.com` and get back credentialed, cross-origin access (cookies, auth) if the app allows that origin or uses `*`.

**Fix:**

- In `Response.response()`, set `Access-Control-Allow-Origin` only from an explicit allowlist (from CORS config), not from the raw request `Origin`, or use `*` only when credentials are **not** sent.
- Set `Access-Control-Allow-Credentials: true` only when the app explicitly enables it in CORS config and when the allowed origin is not `*` (browsers forbid `*` + credentials).

---

### 2. Stack traces and exception details sent to client (when debug/local)

**Where:** `dreema/routing/processing.py` (line 151), `index.py` (line 80), `dreema/responses/response.py` (line 38)

**Issue:** On unhandled exceptions, the framework includes `trace=traceback.format_exc()` in the response. In `response.py`, `trace` is removed only when `settings("environment", 'local')` is **not** in `["debug", "local"]`. So in debug or local mode, full stack traces and file paths are returned to the client.

**Fix:**

- In production, ensure `environment` is never `debug` or `local` (e.g. use `production` or `live`).
- Optionally strip or redact `trace` in all environments and expose detailed errors only via server-side logging.

---

## High

### 3. No request body size limit — DoS

**Where:** `dreema/requests/request.py` — `body()` (async loop over `receive()`)

**Issue:** The body is read in a loop until `more_body` is false with no limit. A client can send an unbounded body and exhaust server memory.

**Fix:** Enforce a maximum body size (e.g. 1–10 MB) and stop reading (and optionally close the connection) when exceeded.

---

### 4. Weak RNG for security-sensitive tokens

**Where:** `dreema/security/encrypt.py` — `generateRandom()`

**Issue:** `generateRandom()` uses `random.choices()` (Python’s non-cryptographic RNG). If this is used for session IDs, CSRF tokens, or password resets, values are predictable.

**Fix:** Use `secrets` for any security-sensitive randomness (e.g. `secrets.token_urlsafe()` or `secrets.token_hex()`). Keep `getSecret()` as-is (it already uses `secrets.token_hex`). Reserve `generateRandom()` for non-security use only and document that.

---

### 5. File upload: extension from Content-Type, no allowlist

**Where:** `dreema/files/parser.py` — `getMultipartKeys()`

**Issue:** The saved file extension is taken from `content.split("/")[1]` (e.g. `image/png` → `png`). There is no allowlist; an attacker can use a type like `application/x-httpd-php` to get `.x-httpd-php` or similar. If the server or a reverse proxy executes scripts by extension, this can lead to code execution. File size is also not limited (DoS).

**Fix:** Use an allowlist of safe extensions (e.g. `png`, `jpg`, `pdf`, `csv`) and map allowed MIME types to those extensions. Reject or ignore unknown types. Enforce a max file size per part and total multipart size.

---

### 6. File operations: path traversal if paths are user-controlled

**Where:** `dreema/files/management.py` — `save()`, `delete()`

**Issue:** `save(destination, newfilename)` and `delete(fullpath)` use paths as given. If the app passes user input (e.g. from multipart `fullPath` or form fields) into these, an attacker can use `../` to write or delete files outside the intended directory.

**Fix:** Validate and constrain paths: resolve to a canonical path and ensure it lies under an allowed base directory (e.g. a single upload root). Do not expose internal paths (e.g. `fullPath`) to the client if they can be sent back; use opaque IDs and map to server paths internally.

---

## Medium

### 7. CORS default allows all origins

**Where:** `dreema/routing/cors.py` — default `allowedOrigins = ["*"]`

**Issue:** If the app does not override CORS config, any origin is allowed. Combined with (1), this makes credentialed cross-origin abuse trivial.

**Fix:** Document that production apps must set explicit `allowedOrigins` and avoid `*` when using cookies or auth. Consider defaulting to an empty list or a single app origin and requiring explicit configuration for cross-origin.

---

### 8. Path parameters not validated by framework

**Where:** `dreema/routing/processing.py` — `matchRoute()`; `request.pathParam()` exposes values as-is

**Issue:** Path segment values (e.g. `/items/:id` → `id`) are passed through unchanged. If an app uses them in file paths, redirects, or raw queries without validation, this can enable path traversal, open redirects, or injection.

**Fix:** Document that path param values are untrusted. Apps should validate (type, format, allowlist) and never use them directly in file paths or redirects. Framework could optionally provide a small helper (e.g. “allowlist of safe chars” or “must match regex”) for common cases.

---

### 9. MongoDB: `include` / `exclude` / `sortfield` not sanitized

**Where:** `dreema/orm/mongo/querybuilder.py` — `readQueryBuilder()` (params: `include`, `exclude`, `sortfield`)

**Issue:** Filter keys are checked for `$` and `.`, but `params["include"]`, `params["exclude"]`, and `params["sortfield"]` are used in projection and sort without rejecting keys that start with `$` or contain `.`. Unusual projection keys could lead to surprising or unsafe behavior depending on driver/version.

**Fix:** Validate every key in `include`, `exclude`, and `sortfield`: reject keys that start with `$` or contain `..` or `.` in a way that could be used for injection. Use a safe identifier pattern (e.g. alphanumeric and underscore only) for sort field and projection keys.

---

### 10. Redis password and connection from env

**Where:** `dreema/redis/actions.py`, `dreema/scheduler/setup.py`

**Issue:** Redis host, port, and password come from `getenv()`. Empty password is allowed. If `.env` is wrong or missing, the app may connect to Redis without auth or to a wrong host (data exposure or abuse).

**Fix:** Document that production must set `REDIS_PASSWORD` and restrict Redis to a trusted network. Optionally warn or refuse to start when password is empty in production.

---

## Low / hardening

### 11. Exception messages in ORM/DB layer

**Where:** `dreema/orm/mysql/queries.py`, `dreema/orm/mongo/queries.py`, `dreema/orm/mysql/connection.py`, `dreema/orm/mongo/connection.py`

**Issue:** Some `trace` or error messages include `type(e).__name__` and `str(e)`. If those are ever returned to the client (e.g. when `trace` is not stripped), they can leak DB or filesystem details.

**Fix:** Rely on the existing `response.py` behavior that strips `trace` when not in debug/local. In production, ensure `environment` is never debug/local. Optionally log full trace server-side and return a generic message to the client.

---

### 12. Scaffolding (cmd) writes files from classname/filename

**Where:** `dreema/helpers/cmd.py` — `Model.create()`, `Controller.create()`, `View.create()`

**Issue:** `filename` is used in `models/{filename}.py` and similar; `classname` is interpolated into generated code. If these ever come from user input (e.g. a dev tool or CLI), path traversal (`../../../etc/passwd`) or code injection (newlines in `classname`) is possible.

**Fix:** Use these only from trusted dev tooling. Sanitize `filename` (e.g. allow only `[a-zA-Z0-9_]`) and `classname` (e.g. single identifier, no newlines or special chars). Reject paths that escape the target directory.

---

## Summary table

| #   | Severity | Area      | Short description                                      |
| --- | -------- | --------- | ------------------------------------------------------ |
| 1   | Critical | CORS      | Origin reflected + credentials true, no allowlist      |
| 2   | Critical | Response  | Stack traces to client in debug/local                  |
| 3   | High     | Request   | No body size limit (DoS)                               |
| 4   | High     | Security  | Weak RNG in `generateRandom()`                         |
| 5   | High     | Files     | Upload extension from Content-Type, no allowlist       |
| 6   | High     | Files     | Path traversal in save/delete if paths user-controlled |
| 7   | Medium   | CORS      | Default allowedOrigins = ["*"]                         |
| 8   | Medium   | Routing   | Path params not validated by framework                 |
| 9   | Medium   | Mongo ORM | include/exclude/sortfield not sanitized                |
| 10  | Medium   | Redis     | Empty password / env config                            |
| 11  | Low      | ORM       | Exception text could leak if trace ever exposed        |
| 12  | Low      | Helpers   | cmd scaffolding path/code injection if user-controlled |

---

**Note:** Authentication and authorization are implemented by the application (e.g. via `setAuthHandler`). The framework does not enforce auth on routes; ensure every sensitive route is protected in app code.
