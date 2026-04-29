# Config

Dreema uses a clear split: **non-secret configuration** in **settings.json**, **secrets** in **.env**, and optional app-level config in **config.py**. This keeps credentials out of version control and makes multi-database setup straightforward.

---

## Settings (non-secrets): settings.json

**settings.json** (or **.settings.json** / **.settings.example.json** in your project root) holds **all configuration that is not secret**: environment name, server port, database connection _structure_ (type, host, port, database name, TLS flag), Redis host/port, etc. **No passwords or usernames** go here — only structure and non-sensitive values.

The framework reads it with **settings(key)** from `dreema.helpers`. Use dot-notation for nested keys: `settings("databases.default.host")`, `settings("serverPort")`, `settings("environment")`.

Example (safe to commit or share):

```json
{
  "environment": "local",
  "serverPort": 8888,
  "databases": {
    "default": {
      "type": "mysql",
      "host": "localhost",
      "port": 3306,
      "database": "Dreema_test",
      "useTls": false
    },
    "app": {
      "type": "mysql",
      "host": "localhost",
      "port": 3306,
      "database": "app_db",
      "useTls": false
    }
  }
}
```

- **default** — Used when you do not pass a connection name (see [Database setup](../database/setup-required.md#multi-database) and [Creating a model](../models/creating-a-model.md#choosing-the-database-connection)).
- **app**, **customers**, etc. — Named connections for [multi-database](../database/setup-required.md#multi-database). You define type, host, port, database here; credentials live in **.env** only.

See [Database setup](../database/setup-required.md) for how the ORM uses `databases.*` and [Multi-database](../database/setup-required.md#multi-database) for switching connections at the model level.

---

## Secrets: .env

**.env** holds **all secrets**: database usernames and passwords, Redis password, and any other sensitive values. It is **not** committed to version control. The framework reads it with **getenv(key, default)** from `dreema.helpers`; if a key is missing in .env, **getenv** falls back to the system environment (e.g. `os.environ`).

**Default database** (connection name `default`):

| Variable      | Description   | Example       |
| ------------- | ------------- | ------------- |
| `DB_TYPE`     | Engine        | `mysql`       |
| `DB_HOST`     | Host          | `localhost`   |
| `DB_PORT`     | Port          | `3306`        |
| `DB_NAME`     | Database name | `Dreema_test` |
| `DB_USER`     | Username      | `root`        |
| `DB_PASSWORD` | Password      | _(secret)_    |
| `DB_USE_TLS`  | Use TLS       | `false`       |

**Named databases** (e.g. connection `app` or `customers`): put credentials in .env with the connection name in the key:

- `DB_APP_USER`, `DB_APP_PASSWORD` for connection **app**
- `DB_CUSTOMERS_USER`, `DB_CUSTOMERS_PASSWORD` for connection **customers**

The connection’s type, host, port, and database name come from **settings.json**; only user and password come from **.env**. See [Multi-database](../database/setup-required.md#multi-database).

**Redis** (if used): `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD` — all from .env.

---

## Define config in config.py

In your project root, **config.py** must expose a **CONFIG** dict. Add any keys and values you need: flags, nested objects, lists, strings, numbers.

Example (structure based on a typical app):

```python
# config.py

CONFIG = {
    "useGunicornLive": False,

    "cors": {
        "allowedOrigins": ["*"],
        "allowedMethods": ["GET", "POST", "PUT", "DELETE"],
        "notAllowedHeaders": [],
        "allowCredentials": True,
    },

    "redisKeys": {
        "SCHOOLS": "schools",
        "USERS": "users",
        "APPINFO": "appInfo",
        "PAYMENTS": "payments",
        "MESSAGES": "messages",
    },

    "files": {
        "maxUploadSize": 1_048_576,
        "allowedFiles": ["*"],
    },

    "featureFlags": {
        "betaDashboard": True,
        "maxExportRows": 10_000,
    },
}
```

You can add or remove keys; the framework does not require specific names. This is your application config, shaped how you want.

---

## Read config with getconfig('name')

Use **getconfig(key)** (from `dreema.helpers`) to read a value by key. The key is the name you used in **CONFIG**. If the value is a dict, you get a dict-like object (Json); otherwise you get the value as-is.

```python
from dreema.helpers import getconfig

# Top-level keys — you get exactly what you set
use_live = getconfig("useGunicornLive")           # False
cors = getconfig("cors")                          # dict: allowedOrigins, allowedMethods, ...
redis_keys = getconfig("redisKeys")               # dict: SCHOOLS, USERS, APPINFO, ...
file_opts = getconfig("files")                    # dict: maxUploadSize, allowedFiles

# Nested values
max_size = getconfig("files").get("maxUploadSize")
schools_key = getconfig("redisKeys").get("SCHOOLS")  # "schools"

# Optional default if key is missing
beta = getconfig("featureFlags", {}).get("betaDashboard", False)
```

Whatever you set under a key is what **getconfig(key)** returns. No transformation beyond making dict values accessible as object-like attributes where applicable.

---

## Where config is used

The framework may read config for built-in behaviour (e.g. **getconfig("cors")** for CORS). Your controllers, models, and utilities can call **getconfig** whenever they need a setting. Keep all such app-level settings in **CONFIG** so they live in one place.

---

## Summary

| Step   | What you do                                                                                 |
| ------ | ------------------------------------------------------------------------------------------- |
| Define | In **config.py**, set **CONFIG = { "key": value, ... }** with any keys and values you need. |
| Read   | In code, call **getconfig("key")** (and optionally **getconfig("key", default)**).          |
| Result | You get back exactly what you set for that key.                                             |

For **non-secret** structure (DB host, port, database name, server port), use **settings.json** and **settings("key")** as above. For **secrets** (DB user, password, Redis password), use **.env** and **getenv("VAR_NAME")**; **getenv** also falls back to the system environment. **config.py** is for application shape and options (e.g. CORS, feature flags) that are not environment-specific secrets.
