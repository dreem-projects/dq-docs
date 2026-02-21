# Returning responses

Controllers should return responses in the **unified envelope** so every endpoint has the same shape: `data`, `message`, `status`, `statuscode`.

## Using `response()`

Import from `dreema.responses`:

```python
from dreema.responses import response, SysCodes
```

- **Success** — `return response(message="Done", status=SysCodes.SETUP_COMPLETED)` or `return response(data=items, message="OK", status=SysCodes.SUCCESS, statuscode=200)`.
- **With payload** — `return response(data=created, message="Created", status=SysCodes.SUCCESS, statuscode=201)`.

You can pass: `data`, `message`, `status`, `statuscode`. The framework fills defaults when omitted (e.g. 200, standard success code).

## Custom envelope

When you need to return an object that is already a full envelope (e.g. validation error with its own `status`, `message`, `data`):

```python
if body.status < 0:
    return response(body, custom=True)
```

`custom=True` tells the framework to use the given object as the response body instead of wrapping it again.

## Returning model results

Some code returns the result of a model call directly (e.g. `return await mod.read(...)`). The framework may wrap that in the unified envelope if the return path supports it; for clarity and consistent status codes, prefer returning explicitly:

```python
items = await mod.read(params={'limit': 5})
return response(data=items, message="OK", status=SysCodes.SUCCESS)
```

## Status codes

- Use **SysCodes** (and optional custom codes from `registers.extendCodes`) for the `status` field.
- Use **statuscode** for the HTTP status (200, 201, 400, 401, 403, 404, 500). The framework sends this as the HTTP status code.
