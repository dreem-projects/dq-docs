# Config

Config is the place you pass **whatever configuration your application needs**. You define it in **config.py** as a **CONFIG** dict; anywhere in the app you read it with **getconfig('name')**. Whatever you set is what you get — no fixed schema.

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

| Step | What you do |
|------|-------------|
| Define | In **config.py**, set **CONFIG = { "key": value, ... }** with any keys and values you need. |
| Read | In code, call **getconfig("key")** (and optionally **getconfig("key", default)**). |
| Result | You get back exactly what you set for that key. |

For environment-specific values (e.g. DB host, port), use **.env** and **getenv("VAR_NAME")**; see your project’s env template. Config is for application shape and options; env is for deployment and secrets.
