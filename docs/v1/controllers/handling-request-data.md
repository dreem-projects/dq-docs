# Handling request data

Controllers get input from the **Request** object: body, query, and headers.

## Body (JSON / form)

Get the parsed body with:

```python
body = await request.body()
# body.data — parsed payload (e.g. dict or object with attributes)
# body.status / body.message — if the framework wraps body parse result
```

Use this for POST/PUT/PATCH payloads. Then validate with `applyRules` or `trimApplyRules` (see [Validation](../guides/validation.md)).

## Query

Get query string with:

```python
query = request.queryParam()
# Use as dict for validation or for query keys (e.g. id, limit, skip)
```

Example: validate `id` from query and pass to read:

```python
body = await request.trimApplyRules({'id': 'required'}, request.queryParam())
if body.status > 0:
    mod = SampleModel()
    return await mod.read(filters={'id': body.data.id})
```

## Validation

- **request.applyRules(rules)** — Validate against the request body (default source).
- **request.trimApplyRules(rules, source)** — Validate and trim unknown keys; **source** can be `request.queryParam()` or the body.

Rules can include: `required`, `int`, `str`, `float`, `list`, `bool`, `nullable`, etc. If validation fails, the returned object has a negative status; return it to the client (e.g. `response(body, custom=True)`).

## Headers

Access headers via the request object (implementation-dependent). Use them for auth tokens or other metadata; the framework does not enforce auth, so the controller must read and validate tokens and return 401/403 when needed.
