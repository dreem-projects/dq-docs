# Auth

Authentication in DQuode is **pluggable**: you register an auth handler; controllers call it and decide how to respond.

## Registering the auth handler

In **registers.py** (or your bootstrap), register a handler with **setAuthHandler**:

```python
from dreema.security import setAuthHandler

def myAuthHandler(request, *args, **kwargs):
    # Validate token, session, etc.; return whatever you want
    # Suggested: data, status, message (optional)
    return Json({"data": user, "status": 100, "message": "OK"})

setAuthHandler(myAuthHandler)
```

The handler receives the **Request** and optional args/kwargs. The return value is **not** fixed: you can return **whatever you want** (e.g. a dict with **data**, **status**, **message**, or a custom shape). The framework does **not** enforce 401/403.

## What the controller receives

When a controller calls auth (e.g. **await request.user()** or **request.user(types=[...])**), it receives **exactly** whatever your auth handler returned. If the handler returns `Json({"data": user, "status": 100, "message": "OK"})`, the controller gets that object. If the handler returns a plain dict, a user object, or something else, the controller gets that. No transformation is applied.

## Using auth in controllers

- Call the registered auth handler (e.g. `await request.user()` or `request.user(types=[...])`).
- Check the result: if auth failed, return an error response (e.g. 401, 403) with the appropriate message; if it succeeded, use the returned data (e.g. `result.data`) as needed.

!!! warning "Auth is not enforced by the framework"
The framework does **not** automatically return 401/403. The controller must check the auth result and return the error response.

## Suggested auth response shape

Using **data**, **status**, and **message** in the auth handler return is **recommended** but **optional**:

- **data** — e.g. the authenticated user (or `None` on failure).
- **status** — e.g. a success code or `SysCodes.INVALID_CREDS` on failure.
- **message** — e.g. `"OK"` or an error message.

Controllers can then branch on `result.status` and use `result.data`. You are free to return a different shape; the controller will receive it as-is.
