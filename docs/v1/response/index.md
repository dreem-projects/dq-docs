# Response

Controllers decide what gets sent back. You can return a plain value (string, dict, bytes, etc.) or use **response()** when you need the standard envelope, a specific HTTP status, or custom headers.

---

## What you can return

| Return style | Use when |
| ------------ | -------- |
| `return value` | Any JSON-serializable value or **bytes**. Bytes are sent with default `Content-Type: application/octet-stream`. |
| `response(data=..., message=..., status=...)` | You want the **data**, **message**, **status** envelope. |
| `response(value, custom=True, statuscode=..., headers={...})` | Your own body with a specific HTTP status and headers (e.g. binary with `Content-Type: image/jpeg`). |

The framework does not override your return value or status. HTTP status is 200 unless you pass **statuscode**.

---

## Unified envelope

When you use **response()** without **custom=True**, the body is a JSON object with:

- **data** — payload (list, object, or null)
- **message** — short human-readable text
- **status** — application-level code (e.g. SysCodes)
- **trace** — optional; stripped when ENVIRONMENT is not debug or local

**statuscode** is only used for the HTTP status line; it is not included in the body.

---

## Headers and body

You can send any response headers via **response(..., headers={...})**: for example `Content-Type`, `Content-Disposition`, `Cache-Control`, `Set-Cookie`, or custom headers. They are merged with the framework’s default CORS and content-type headers.

The body you return must match the headers you set. If you set `Content-Type: image/jpeg`, the body must be JPEG bytes. If you set `Content-Type: application/json`, the body must be JSON or a value the framework can serialize to JSON. Mismatched headers and body cause broken rendering or parsing.

---

## Why use one shape?

- **Frontend** — One parser and consistent branching on **status** and HTTP status for all endpoints.
- **Backend** — ORM, validation, and routing share the same envelope; easier logging and error handling.
- **API** — Same top-level keys everywhere. See [Handling function responses](../controllers/handling-function-responses.md) for the full rationale.

---

## How it works

1. The controller runs and returns a value or calls **response(...)**.
2. The framework serializes the body (or sends it as-is for **custom=True** and bytes), sets HTTP status from **statuscode** if provided, and sends the body and headers.

**Next:** [Response functions](response-functions.md) — **response()**, **SysCodes**, **SysMessages**, **custom**, **headers**.
