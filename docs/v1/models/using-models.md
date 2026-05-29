# Using models

Models are used in **controllers** (and anywhere you need to talk to the database). You instantiate a model, then call its ([database operations](../database/database-functions.md)) with the same API for ([supported Database systems](../getting-started/supported-databases.md)).

## Basic usage

Create an instance and call ([database operations](../database/database-functions.md)) as needed. By default the model uses the **default** database; to use a [multi-database](../database/setup-required.md#multi-database) connection, pass **connection='name'** when creating the model.

```python title="Performing simple CRUD functionality"
from models.sampleModel import SampleModel

# Default database
mod = SampleModel()

# Or a named database (e.g. "app" defined in settings.py + .env)
mod = SampleModel(connection='app')

# Create a record
res = await mod.create(data=body)

# Read (with optional filters and params)
res = await mod.read(filters={'id': id}, params={'limit': 5})

# Update
res = await mod.update(filters={'id': id}, data={'name': name})

# Delete
res = await mod.delete(filters={'id': id})
```

Each method is **async** and returns a result object with `data`, `status`, and `message`. Check `res.status` to see if the operation succeeded before using `res.data`.

## Where to use them

- **Controllers** — Instantiate the model in your route handler, call the appropriate method, then return a response (e.g. via `response(res, custom=True)`).
- **DBware** — If you use the middleware, it looks up models by key from your model list; your model must be registered there. Direct model use does not require registration.

For filter syntax, params (`limit`, `sort`, etc.), and return shapes, see [Database operations](../database/read.md) and [Filters and params](../database/filters-and-params.md). For defining and using multiple databases, see [Database setup: Multi-database](../database/setup-required.md#multi-database) and [Config: settings and .env](../guides/config.md#settings-non-secrets-settingsjson).
