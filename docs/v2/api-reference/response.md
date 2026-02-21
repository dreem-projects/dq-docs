# API reference — Response

## response

```python
from dreema.responses import response

response(data=None, message=None, status=None, statuscode=None, trace=None, headers=None, custom=False)
```

- **data** — Response payload (any JSON-serializable value).
- **message** — Human-readable message.
- **status** — Application-level code (e.g. **SysCodes** or custom from **extendCodes**).
- **statuscode** — HTTP status code (e.g. 200, 201, 400, 401, 403, 404, 500).
- **trace** — Optional debug info; typically stripped when **ENVIRONMENT** is not debug/local.
- **headers** — Optional dict of response headers.
- **custom** — If **True**, the first argument is treated as the full response body (e.g. validation error object); other kwargs may be ignored.

Returns a **Response** (or equivalent) that the framework sends with the unified envelope: **data**, **message**, **status**, **statuscode**.

## SysCodes, SysMessages

- **SysCodes** — Constants for status (e.g. SETUP_COMPLETED, SUCCESS, READ_SUCCESS, CREATE_FAILED, NO_RECORD).
- **SysMessages** — Default messages for those codes.

Use in **response(status=SysCodes.READ_SUCCESS, message=SysMessages.READ_SUCCESS)**. **extendCodes** and **extendMessages** (in registers) add or override codes and messages.
