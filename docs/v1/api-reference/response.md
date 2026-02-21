# API reference — Response

## response

```python
from dreema.responses import response

response(data=None, message=None, status=None, statuscode=None, trace=None, headers=None, custom=False)
```

- **data** — Response payload (any JSON-serializable value, or **bytes** when **custom=True** for binary).
- **message** — Human-readable message.
- **status** — Application-level code (e.g. **SysCodes** or custom from **extendCodes**).
- **statuscode** — HTTP status code (e.g. 200, 201, 400, 401, 403, 404, 500). Used by the framework for the HTTP status line only; **not echoed in the response body** (stripped before sending).
- **trace** — Optional debug info; typically stripped when **ENVIRONMENT** is not debug/local.
- **headers** — Optional dict of response headers (e.g. **Set-Cookie**, **Content-Type** for binary). For **custom=True** with **bytes**, set **Content-Type** (e.g. `image/jpeg`, `application/pdf`) so the response uses the correct media type.
- **custom** — If **True**, pass **whatever you want** as the first argument: any dict, list, JSON-serializable value, or **bytes** (binary). The framework sends it as-is. For **bytes**, pass **headers={"Content-Type": "image/jpeg"}** (or the appropriate media type) so the client receives the correct Content-Type.

Returns a **Response** (or equivalent) that the framework sends. The body contains **data**, **message**, **status**, and optionally **trace** (and **statuscode** is not included in the body).

## SysCodes, SysMessages

- **SysCodes** — Constants for status (e.g. SETUP_COMPLETED, SUCCESS, READ_SUCCESS, CREATE_FAILED, NO_RECORD).
- **SysMessages** — Default messages for those codes.

Use in **response(status=SysCodes.READ_SUCCESS, message=SysMessages.READ_SUCCESS)**. **extendCodes** and **extendMessages** (in registers) add or override codes and messages.
