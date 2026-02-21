# CORS

CORS is configured via app config and applied in the **Dispatcher** before route matching.

## Configuration

Use **getconfig("cors")** to supply CORS settings. Typical keys:

- **allowedOrigins** — List of origins (e.g. `["https://app.example.com"]`).
- **allowedMethods** — Allowed HTTP methods (e.g. `["GET", "POST", "PUT", "DELETE", "OPTIONS"]`).
- **notAllowedHeaders** or **allowedHeaders** — Header allowlist/blocklist (name depends on implementation).

Defaults are applied when a key is missing. Check the Dispatcher or CORS module for exact key names and defaults.

## When it runs

The Dispatcher handles CORS **before** resolving the route:

- **Preflight** — OPTIONS requests get the appropriate CORS headers and a response without running the route handler.
- **Actual request** — CORS headers are added to the response according to the config.

So CORS does not change path or method matching; it only adds headers and responds to OPTIONS.
