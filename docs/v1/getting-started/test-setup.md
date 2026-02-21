# Test the setup

After starting the server, verify that the app responds and returns the **unified response structure**. You can do this without a `.env` file; the server runs on port **8888** by default.

## 1. Hit an endpoint

If your app has a root or health route (e.g. `GET /` in `views/endpoints.py`), run:

```bash
curl http://localhost:8888/
```

If you set a different port in `.env` (e.g. `SERVER_PORT=8000`), use that port instead.

## 2. Confirm the response shape

DQuode responses use a consistent envelope. You should see JSON with at least:

- **`data`** — Payload (can be `null` on error).
- **`message`** — Human-readable message (e.g. "Setup completed").
- **`status`** — Application-level code (e.g. `100` for success, negative for errors).
- **`statuscode`** — HTTP status (e.g. `200`).

Example success response:

```json
{
  "data": null,
  "message": "Setup completed",
  "status": 100,
  "statuscode": 200
}
```

If you see this structure, the server is running and the routing and response pipeline are working.
