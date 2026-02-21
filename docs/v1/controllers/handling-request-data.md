# Handling request data

Controllers get input from the **Request** object: body, query/params, and headers.

## Body (JSON / form)

Get the parsed body with:

```python
body = await request.body()
# body.data — parsed payload (e.g. dict or object with attributes)
# body.status / body.message — if the framework wraps body parse result
```

Use this for POST/PUT/PATCH payloads. Then validate with `applyRules` or `trimApplyRules` (see [Validation](../guides/validation.md)).

## Query / params

Get query string (or route params) with:

```python
params = request.params()
# Use as dict for validation or for read params (e.g. id, limit, skip)
```

Example: validate `id` from query and pass to read:

```python
body = await request.trimApplyRules({'id': 'required'}, request.params())
if body.status > 0:
    mod = SampleModel()
    return await mod.read(filters={'id': body.data.id})
```

## Validation

- **request.applyRules(rules)** — Validate against the request body (default source).
- **request.trimApplyRules(rules, source)** — Validate and trim unknown keys; **source** can be `request.params()` or the body.

Rules can include: `required`, `int`, `str`, `float`, `list`, `bool`, `nullable`, etc. If validation fails, the returned object has a negative status; return it to the client (e.g. `response(body, custom=True)`).

## Headers

Access headers via the request object (implementation-dependent). Use them for auth tokens or other metadata; the framework does not enforce auth, so the controller must read and validate tokens and return 401/403 when needed.
