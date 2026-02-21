# Auth

Authentication in DQuode is **pluggable**: you register an auth handler; controllers call it and decide how to respond.

## Registering the auth handler

In **registers.py** (from `registers.template.py`):

```python
from dreema.helpers import setAuthHandler

def myAuthHandler(request):
    # Validate token, session, etc.; return a result object
    # with data (e.g. user), status, message
    return Json({"data": user, "status": 100, "message": "OK"})

setAuthHandler(myAuthHandler)
```

The handler receives the **Request** and returns a result (e.g. Json with **data**, **status**, **message**). The framework does **not** enforce 401/403.

## Using auth in controllers

- **request.user()** — Call the registered auth handler (e.g. `await request.user()` or `request.user(types=[...])`). You get back the result object (e.g. user data, or error status/message).
- The **controller** must check this result:
  - If auth failed, return an error response (e.g. 401 Unauthorized, 403 Forbidden) with the appropriate message.
  - If auth succeeded, continue and use `user` (or equivalent) as needed.

!!! warning "Auth is not enforced by the framework"
    The framework does **not** automatically return 401/403. The controller must check the auth result and return the error response.

## Auth response shape

The auth handler typically returns an object with **data** (e.g. user), **status**, and **message**. Controllers use this to decide whether to proceed or return an error.
