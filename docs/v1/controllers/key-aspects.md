# Controller Key Aspects

💡 Controllers are the core of your app’s logic — _exactly_ what happens for each endpoint.

---

## What is a Controller?

A controller decides the action for every request. Its main jobs:

- 📨 **Receive the request**
- 🧰 **Validate and authorize** input
- 🧠 **Run your business logic**
- 📤 **Assemble the response**

Controllers are the **entry-point for app behavior** — keep them focused and readable!

---

## Only async Functions

```py
async def my_handler(request):
    ...
```

⬆️ **Always async:**  
All controller handlers **must** be `async`.  
Use `await` for reading request data or database operations.

---

## Request is Always the 1st Parameter

Each handler signature is always:

```py
async def my_handler(request):
    # Get data from the request object
```

No auto-injected dependencies — read everything via the **request**:

- `await request.body()`
- `request.queryParam()`
- `request.headers()`

---

## You Manage Auth (401/403)

> **No magic. Your controller, your rules.**

If you use an auth handler (like `registers.setAuthHandler`), it’s up to you to:

1. Call your own authentication logic (e.g. validate a token)
2. On failure, return the proper response and status — for example:

   ```py
   if not user:
       return response({"error": "Unauthorized"}, statuscode=401)
   ```

**You** explicitly decide _when_ to return 401 or 403.

---

## 🔍 Explicit Input Validation

Validate what you need — for example, with:

```py
body = await request.trimApplyRules({
    "id": "required,int",
    "name": "str"
})
```

If validation fails (`body.status < 0`), return:

```py
return body
```

_body by default contains data, message and status. This pattern ensures clients always get clear, shaped error responses._

---

## Shape Your Response

Controllers control **what you send**. You can return **anything** the framework can send: JSON-serializable values, **binary (bytes)**, or use **response()** for the standard envelope or full control over status and headers.

- **Plain value** — the framework sends it as-is with an appropriate default `Content-Type`:
  - **str** → `text/plain; charset=utf-8`
  - **bytes** → `application/octet-stream` (binary)
  - **dict / list** → JSON with `application/json`

  ```py
  return "Hello"
  return {"name": "dede"}
  return image_bytes   # binary; browser may download or show as generic binary
  ```

  For **binary with a specific type** (e.g. image so the browser renders it), use **response()** with **custom=True** and **headers**:

  ```py
  return response(image_bytes, custom=True, statuscode=200, headers={"Content-Type": "image/jpeg"})
  ```

- **Standard envelope** (when you want **data**, **message**, **status** and optional **statuscode**):

  ```py
  return response(data=..., message=..., status=..., statuscode(optional)=200)
  ```

- **Custom shape/status** — your own body and/or status without the envelope; **headers** let you send anything (e.g. binary, PDF, custom Content-Type):
  ```py
  return response(custom_err, custom=True, statuscode=404)
  return response(pdf_bytes, custom=True, headers={"Content-Type": "application/pdf"})
  ```

_The framework **never** overrides your return value or status._  
For more, see [Handling function responses](handling-function-responses.md).

---
