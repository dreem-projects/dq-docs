# Response functions

Controllers can return data in two ways: **return any value** (your own response), or use **response()** when you want the standard envelope or need to set the HTTP status code. This page explains **response()**, **SysCodes**, and **SysMessages**.

!!! tip "You can return your own response"
You are **entitled to** `return value` — the framework sends whatever you return (dict, list, str, **bytes**, etc.). **Bytes** are sent as binary with `Content-Type: application/octet-stream`; for images or other binary with a specific type, use **response(value, custom=True, headers={"Content-Type": "image/jpeg"})** (or the appropriate media type). Use **response()** when you want the **data**, **message**, **status** envelope or need **statuscode**. The default HTTP status is **mostly 200**; you are in charge.

!!! tip "Import"
`python
    from dreema.responses import response, SysCodes, SysMessages
    `

---

## response()

Builds the unified response envelope sent to the client.

```python
response(
    data=None,
    message=None,
    status=None,
    statuscode=200,
    trace=None,
    headers=None,
    custom=False
)
```

| Argument       | Description                                                                                                                                                                                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **data**       | Payload (any JSON-serializable value): list, dict, id, or `null`.                                                                                                                                                                                                              |
| **message**    | Human-readable string (e.g. "Operation successful", "Record Not Found").                                                                                                                                                                                                       |
| **status**     | Application-level code: use **SysCodes** or a custom code from **extendCodes**.                                                                                                                                                                                                |
| **statuscode** | HTTP status (200, 201, 400, 401, 403, 404, 500). Default 200. **Not echoed in the response body** — the framework uses it only for the HTTP status line and strips it before sending.                                                                                          |
| **trace**      | Debug info (e.g. exception text). **Stripped** when **ENVIRONMENT** is not `debug` or `local`.                                                                                                                                                                                 |
| **headers**    | Optional dict of response headers (e.g. `Set-Cookie`, **Content-Type** for binary). For **custom=True** with **bytes**, set **Content-Type** (e.g. `image/jpeg`, `application/pdf`) so the client receives the correct media type.                                                                 |
| **custom**     | If **True**, pass **whatever you want** as the first argument: any dict, list, JSON-serializable value, or **bytes** (binary). The framework sends it as-is. Use for ORM/validation forwarding, custom JSON, or **binary** (images, PDFs) — with **headers** for the right Content-Type. |

**Returns:** An object the framework serializes to JSON and sends with the correct HTTP status.

### Examples

```python
# Success with payload
return response(data=items, message=SysMessages.READ_SUCCESS, status=SysCodes.READ_SUCCESS, statuscode=200)

# Success, minimal
return response(message="Done", status=SysCodes.SETUP_COMPLETED)

# Error with trace (trace only sent in debug/local)
return response(data=None, message="Validation failed", status=SysCodes.ATTR_MISSING, statuscode=400, trace=str(e))

# Forward ORM or validation result as-is
return response(ormResult, custom=True)

# Your own body + specific HTTP status (you must use response() for statuscode)
return response({"done": True, "id": new_id}, custom=True, statuscode=201)

# Binary (e.g. image) with correct Content-Type so the browser renders it
return response(image_bytes, custom=True, statuscode=200, headers={"Content-Type": "image/jpeg"})
```

---

## SysCodes

Numeric constants for the **status** field. Used by the framework (ORM, validation, CORS, routing) and by your controllers.

| Category                | Examples                                                                                                              |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **Success**             | `OP_COMPLETED`, `SETUP_COMPLETED`, `READ_SUCCESS`, `CREATE_SUCCESS`, `UPDATE_SUCCESS`, `DELETE_SUCCESS`, `ATTR_FOUND` |
| **Failure**             | `OP_FAILED`, `READ_FAILED`, `CREATE_FAILED`, `NO_RECORD`, `ATTR_MISSING`, `DB_CONNECTION_FAILED`                      |
| **HTTP / routing**      | `ENDPOINT_NOT_FOUND`, `UNALLOWED_METHOD`, `ENDPOINT_FUNC_FAIL`                                                        |
| **Auth / CORS / files** | `INVALID_CREDS`, `CORS_ORIGIN_NOT_ALLOWED`, `FILE_UPLOAD_FAILED`                                                      |

Success codes are typically positive; error codes negative. Use **registers.extendCodes** to add or override codes. See [API reference — Response](../api-reference/response.md) or the framework source for the full list.

---

## SysMessages

String constants that match **SysCodes**. Use them for the **message** field so clients get consistent text.

```python
return response(
    data=items,
    message=SysMessages.READ_SUCCESS,
    status=SysCodes.READ_SUCCESS
)
```

Use **registers.extendMessages** to add or override messages. Keeping **SysCodes** and **SysMessages** in sync makes frontend i18n and error handling predictable.

---

## custom=True — pass whatever you want

With **custom=True**, you pass a **single argument** — any structure or **binary** — and the framework sends it as the response body **as-is**. No wrapping, no stripping. Use it to:

- **Forward an existing envelope:** ORM results or validation errors that already have **data**, **message**, **status** (and optionally **trace**).
- **Return a fully custom payload:** Any dict, list, or JSON-serializable value — your own keys and shape.
- **Send binary (images, PDFs):** Pass **bytes** and set **headers={"Content-Type": "image/jpeg"}** (or the appropriate media type) so the client receives the correct Content-Type and the browser can render or handle the file correctly.

```python
# Forward ORM result
res = await model.read(params={"limit": 5})
return response(res, custom=True)

# Forward validation error
body = await request.applyRules({"email": "required,str"})
if body.status < 0:
    return response(body, custom=True)

# Send your own structure
return response({"items": [...], "total": 42, "page": 1}, custom=True)
```

The client receives exactly what you pass. Controllers can use **custom** whenever the default envelope is not what you need.

---

## headers

Pass extra response headers (e.g. cookies, **Content-Type** for binary, or custom headers):

```python
# Cookies or custom headers
return response(
    data=payload,
    message=SysMessages.OP_COMPLETED,
    status=SysCodes.OP_COMPLETED,
    headers={"Set-Cookie": "session=abc; Path=/"}
)

# Binary with correct media type (e.g. image, PDF)
return response(image_bytes, custom=True, headers={"Content-Type": "image/jpeg"})
```

The framework merges these with default CORS and content-type headers. For **custom=True** with **bytes**, always set **Content-Type** in **headers** so the response uses the correct media type.

---

For the full envelope structure and rationale, see [Handling function responses](../controllers/handling-function-responses.md). For the exact API, see [API reference — Response](../api-reference/response.md).
