# Handling function responses

Every controller response can follow a **unified envelope** (data, message, status) when you use **response()** — but you are **not required** to. This page explains when to use the envelope, each field, and why the structure exists.

!!! info "Your choice what to return"
You can **return any value** from a controller: `return value`. The framework sends it — including **bytes** (binary), which are sent with `Content-Type: application/octet-stream`. Use **response()** when you want the **data**, **message**, **status** envelope, need to set **statuscode**, or need to send **binary with the right headers** (e.g. **response(image_bytes, custom=True, headers={"Content-Type": "image/jpeg"})** so the browser renders the image). The HTTP status for a controller that runs is **mostly 200**; you are in charge of what you return and what status you set.

!!! info "Why a unified envelope?"
When you do use it, a single response shape improves **frontend integration** (one parser, consistent handling), **backend error catching** (status and trace everywhere), and **API consistency** across endpoints and environments.

---

## Response structure

All non-custom responses include these top-level keys in the **body**:

| Field       | Type           | Description                                                                                             |
| ----------- | -------------- | ------------------------------------------------------------------------------------------------------- |
| **data**    | any            | The payload: result set, created/updated record, or `null` when there is no body (e.g. delete success). |
| **message** | string         | Human-readable message (e.g. "Operation successful", "Record Not Found").                               |
| **status**  | number         | Application-level code from **SysCodes** (or custom codes). Used for branching and logging, not HTTP.   |
| **trace**   | string \| null | Optional debug information (exception text, stack). **Stripped in production** (see below).             |

**statuscode** is a **key you pass** to `response(..., statuscode=201)` so the framework knows which HTTP status to send. It is **not echoed in the response body** — the framework uses it for the HTTP status line only and strips it before sending. Clients see the status via the HTTP response (e.g. 200, 404), not as a JSON key.

Example success (what the client actually receives in the body):

```json
{
  "data": { "id": 1, "name": "Alice" },
  "message": "Read operation successful",
  "status": 200
}
```

Example error (with trace in debug/local; statuscode is still not in the body):

```json
{
  "data": null,
  "message": "CREATE operation failed",
  "status": -302,
  "trace": "IntegrityError: duplicate key value..."
}
```

---

## Field-by-field

### data

- **Purpose:** Carry the actual result — list of records, single object, created id, or `null`.
- **Frontend:** Use this for rendering (tables, forms) and for follow-up requests (e.g. created id).
- **Backend:** ORM and controllers put results here; validation errors may put error details here when using `custom=True`.

### message

- **Purpose:** Short, human-readable description of the outcome.
- **Frontend:** Display in toasts, banners, or fallback text (e.g. "Record Not Found", "Operation completed").
- **Backend:** Comes from **SysMessages** or your own strings; keep it stable so the frontend can match or i18n.

### status

- **Purpose:** Application-level outcome code (numeric). Success codes are typically positive; error codes negative (e.g. **SysCodes**).
- **Frontend:** Branch on `status` (e.g. `status === SysCodes.READ_SUCCESS`) for routing, retry, or UI state instead of parsing `message`.
- **Backend:** Every ORM call and many framework paths set `status`; use it in logs and monitoring. You can extend codes via **registers.extendCodes**.

### statuscode

- **Purpose:** You pass **statuscode** to `response(..., statuscode=201)` so the framework knows which HTTP status to send. The framework uses it **only for the HTTP response line** (e.g. `HTTP/1.1 201 Created`). It is **not included in the response body** — the framework strips it before sending, so the client never sees a `statuscode` key in the JSON.
- **Frontend:** Read the HTTP status from the response (e.g. 401 → redirect to login, 404 → not-found page). Do not expect a `statuscode` field in the body.
- **Backend:** Set via `response(..., statuscode=201)`. Defaults to 200 when omitted. In controllers, use **statuscode** when you need a specific HTTP status; the body carries **data**, **message**, **status**, and optionally **trace**.

### trace

- **Purpose:** Debug information: exception message, stack trace, or other technical detail.
- **Frontend:** In **debug** or **local** only: show in dev tools or error panels; never rely on it in production UI.
- **Backend:** The framework sets `trace` on errors (e.g. ORM, validation). It is **removed from the response body** when **ENVIRONMENT** is not `debug` or `local`, so production clients never see it.

!!! warning "Trace is environment-sensitive"
`trace` is stripped before sending the response when `ENVIRONMENT` is not `debug` or `local`. Use it for local debugging and logs; do not expose it to end users in production.

---

## Rationale

### Frontend integration

- **One parser:** Every endpoint returns the same keys. The frontend can read `data`, `message`, `status`, and `statuscode` once and handle all endpoints uniformly.
- **Stable branching:** Use `status` (and optionally `statuscode`) for flow control (success, not found, validation error, server error) instead of string matching on `message`.
- **Predictable errors:** Error responses still have `data`, `message`, `status`; the frontend can show a generic error view or map `status` to specific actions.

### Backend error catching

- **Consistent shape:** ORM, validation, CORS, and routing all return the same envelope. Middleware and dispatchers can log or react on `status` and `trace` without special cases.
- **Trace for debugging:** When something fails, `trace` (in debug/local) carries the exception so you can fix issues without guessing.
- **Monitoring:** Status codes (and statuscode) are easy to aggregate for alerts and dashboards.

### API consistency

- **Same contract everywhere:** New endpoints and new developers follow the same pattern. Documentation and client code stay simple.
- **custom — pass whatever you want:** Use `response(..., custom=True)` to send **any structure or binary** as the response body. Pass a single argument (e.g. ORM result, validation error, any dict/list, or **bytes** for images/PDFs); the framework sends it as-is. For **bytes**, pass **headers={"Content-Type": "image/jpeg"}** (or the appropriate media type) so the client receives the correct Content-Type.

---

## Using the envelope in controllers

Return responses with **response()** from `dreema.responses`:

```python
from dreema.responses import response, SysCodes, SysMessages

# Success with payload
return response(data=items, message=SysMessages.READ_SUCCESS, status=SysCodes.READ_SUCCESS, statuscode=200)

# Error with optional trace (trace only sent in debug/local)
return response(data=None, message="Validation failed", status=SysCodes.ATTR_MISSING, statuscode=400, trace=str(e))

# Forward ORM/validation result or any custom structure
return response(result, custom=True)
```

**Note:** The **statuscode** you pass is used only for the HTTP status line; it does not appear in the JSON body. The client sees the status from the HTTP response (e.g. 200, 404), not as a body key.

### Returning binary (images, files)

- **Plain bytes:** `return image_bytes` — the framework sends the body as binary with `Content-Type: application/octet-stream`. The browser may offer to download or display it depending on context.
- **Binary with correct type:** Use **response()** with **custom=True** and **headers** so the client gets the right media type (e.g. image renders in the browser):

```python
with open("/path/to/image.jpeg", "rb") as f:
    image = f.read()
return response(image, custom=True, statuscode=200, headers={"Content-Type": "image/jpeg"})
```

See [How to use](using-controllers.md#returning-responses) for more examples and [API reference — Response](../api-reference/response.md) for the full `response()` signature and **SysCodes** / **SysMessages**.
