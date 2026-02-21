# Request functions

The **Request** object exposes these methods. Use them in controller handlers to read input, validate, and inspect the request.

!!! tip "Import"
Handlers receive the request as the first argument: `async def myHandler(request: Request)`. Type-hint with `Request` from `dreema.requests`.

---

## Reading input

### body()

**Async.** Fetches and parses the request body.

```python
body = await request.body()
# body.data — parsed payload (dict for JSON; structure may vary for multipart)
# body.status, body.message — set if the framework wraps parse result
```

- **JSON:** Parsed into a dict; access fields via `body.data` or the object’s attributes.
- **Multipart:** Handled by the framework; result structure is implementation-dependent.
- **No body / missing Content-Type:** May return an empty dict. Validate before use.

Use for POST/PUT/PATCH payloads. Then validate with [applyRules / trimApplyRules](#validation).

---

### params()

Returns query string (and optionally route) parameters as a dict-like object.

```python
params = request.params()
# e.g. ?id=1&limit=10  →  params.get("id"), params.get("limit")
```

Use for GET query args or route params. Pass to `trimApplyRules(rules, request.params())` to validate query input. See [Handling request data](../controllers/handling-request-data.md).

---

## Validation

### applyRules(rules [, source])

**Async.** Validates **rules** against a **source**. Default **source** is the request body (`await self.body()`).

```python
body = await request.applyRules({"name": "required,str", "age": "required,int"})
if body.status < 0:
    return response(body, custom=True)
# body.data — validated body (all keys present)
# body.status == SysCodes.ATTR_FOUND on success
```

- **rules** — Dict mapping field names to rule strings: `required`, `int`, `str`, `float`, `list`, `bool` (comma-separated).
- **source** — Optional. If omitted, body is used. Can pass `request.params()` to validate query params.
- **Returns** — Object with **data**, **status**, **message** (and **trace** on error). On failure, **status** is negative (e.g. `SysCodes.ATTR_MISSING`); return it to the client (e.g. `response(result, custom=True)`).

---

### trimApplyRules(rules [, source])

**Async.** Like **applyRules**, but the returned **data** contains **only** the keys defined in **rules**; unknown keys are stripped.

```python
body = await request.trimApplyRules({"id": "required,int"}, request.params())
if body.status > 0:
    id = body.data.id  # only validated keys present
```

Use when you want a whitelist of allowed fields (e.g. query params or body) and avoid passing through extra keys. **source** can be body or `request.params()`. Rules can include **nullable**. See [Validation](../guides/validation.md).

---

## Request metadata

### method()

Returns the HTTP method as a string (e.g. `"GET"`, `"POST"`, `"PUT"`, `"DELETE"`).

```python
if request.method() != "POST":
    return response(message="Method not allowed", statuscode=405)
```

---

### path()

Returns the request path (e.g. `"/items"`, `"/items/1"`).

```python
path = request.path()
```

---

### headers()

Returns the request headers as a dict-like object (keys: header names; values: header values).

```python
headers = request.headers()
content_type = headers.get("content-type")
# Use for auth tokens, content negotiation, etc.
```

Use for auth tokens (e.g. `Authorization`), content type, or other metadata. The framework does not enforce auth; the controller must read and validate tokens and return 401/403 when needed.

---

### auth()

Returns a parsed view of the `Authorization` header: **type** (e.g. `"Bearer"`) and **value** (e.g. the token). If missing or invalid, **type** and **value** may be `None`.

```python
auth = request.auth()
# auth.type, auth.value
```

---

## Auth and context

### user([types, ...])

**Async.** Calls the registered auth handler (e.g. via **registers.setAuthHandler**). Returns the auth result (e.g. user data, status, message).

```python
user = await request.user(types=["admin", "student"])
# Use user data or check status; return 401/403 if not authorized
```

Optional arguments (e.g. **types**, **roles**, **permissions**) are passed to the auth handler. Implementation depends on your auth setup. See [Auth](../guides/auth.md).

---

## Helpers

| Function        | Description                                                       |
| --------------- | ----------------------------------------------------------------- |
| **client()**    | Returns client host and port (e.g. for logging or rate limiting). |
| **server()**    | Returns server host and port.                                     |
| **scheme()**    | Returns the scheme (e.g. `"http"`, `"https"`).                    |
| **http()**      | Returns the HTTP version.                                         |
| **asgi()**      | Returns ASGI scope spec info.                                     |
| **entryTime()** | Returns the time the request was received.                        |

---

For the full API contract, see [API reference — Request](../api-reference/request.md).
