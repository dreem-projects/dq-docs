# Config

Configuration is centralized: environment variables, app config, and registers.

## .env and getenv

- Copy **.env.template** to **.env** and set values.
- In code, use **getenv** (from `dreema.helpers` or similar) to read env vars: e.g. `getenv("SERVER_PORT", "8888")`, `getenv("DB_TYPE")`, `getenv("ENVIRONMENT", "local")`.

## config.CONFIG and getconfig

- **config.CONFIG** — A dict or object holding app-level config (e.g. CORS, feature flags).
- **getconfig(key)** — Returns the value for **key** from this config (e.g. `getconfig("cors")` for CORS settings).

## registers.py

- **setAuthHandler(handler)** — Register the auth handler used by **request.user()**.
- **extendCodes(codes_dict)** — Add or override system status codes (backend–frontend contract).
- **extendMessages(messages_dict)** — Add or override system messages.

Place these in **registers.py** (or the file that runs at app init) so they are applied before handling requests.
