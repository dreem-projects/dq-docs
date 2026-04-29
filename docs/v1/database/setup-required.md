# Database setup required

Before working with the ORM, you need either **.env** (for the default connection) or **settings.json** plus **.env** (for structure and secrets). Models must specify the table/collection name and can optionally specify which **connection** to use for multi-database setups.

---

## Default connection: .env

For a single default database, put credentials and connection details in **.env** (see [Config: .env](../guides/config.md#secrets-env)):

| Variable      | Description                                                                     | Example     |
| ------------- | ------------------------------------------------------------------------------- | ----------- |
| `DB_TYPE`     | Engine: [Supported database systems](../getting-started/supported-databases.md) | `mysql`     |
| `DB_HOST`     | Database host                                                                   | `localhost` |
| `DB_PORT`     | Port                                                                            | `3306`      |
| `DB_NAME`     | Database name                                                                   | `mydb`      |
| `DB_USER`     | User                                                                            | `dbuser`    |
| `DB_PASSWORD` | Password                                                                        | _(secret)_  |
| `DB_USE_TLS`  | Optional; use TLS                                                               | `false`     |

The ORM reads these when you use a model with the default connection (no connection name, or `connection='default'`).

---

## Optional: structure in settings.json

You can define the **default** (and any extra) connection in **settings.json** so all non-secret values live in one place. In that case, **.env** only needs **DB_USER** and **DB_PASSWORD** for the default connection; type, host, port, and database name come from [settings](../guides/config.md#settings-non-secrets-settingsjson). Named connections (see below) always use settings for structure and .env for credentials.

---

## Multi-database

You can use **multiple databases** in the same app: define each connection in **settings.json** (non-secrets), put credentials in **.env**, and choose the connection when you create the model instance.

### 1. Define connections in settings.json

In **settings.json**, under `databases`, add one entry per connection. Use **default** for the default connection, and any other name (e.g. **app**, **customers**, **analytics**) for the rest. Do **not** put user or password here — only type, host, port, database, and useTls.

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

### 2. Put credentials in .env

- **Default connection:** `DB_USER`, `DB_PASSWORD` (and optionally `DB_TYPE`, `DB_HOST`, `DB_PORT`, `DB_NAME` if you are not defining default in settings).
- **Named connection `app`:** `DB_APP_USER`, `DB_APP_PASSWORD`
- **Named connection `customers`:** `DB_CUSTOMERS_USER`, `DB_CUSTOMERS_PASSWORD`

The pattern is `DB_<CONNECTION_NAME>_USER` and `DB_<CONNECTION_NAME>_PASSWORD`, with the connection name in UPPER*SNAKE_CASE (e.g. **app** → `DB_APP*\*`).

### 3. Set the connection when initializing the model

At the **model level**, pass the connection name into the constructor. The model’s `__init__` should accept a `connection` argument and pass it to `super().__init__(connection)`; then when you instantiate the model, pass the name of the connection you want.

**In your model** (see [Creating a model](../models/creating-a-model.md#choosing-the-database-connection)):

```python
class SampleModel(database.Database):
    tablename = 'Dreema_sample'

    def __init__(self, connection='default'):
        super().__init__(connection)
        self.setTable(self.tablename)
```

**In controllers or elsewhere:**

```python
# Use default database
mod = SampleModel()

# Use the "app" database (defined in settings.json + .env)
mod = SampleModel(connection='app')
```

This keeps configuration in one place (settings + .env) and makes switching databases a single argument at the model level — no code changes per connection.

---

## Model: setTable(tablename)

Each model must call **setTable(self.tablename)** in `__init__` so all ([database operations](database-functions.md)) target the correct table or collection. Use the `connection` parameter as above when you have multiple databases.

- For SQL backends ([Supported database systems](../getting-started/supported-databases.md)) — The table must exist; Dreema does not create tables or run migrations.
- For document backends — The collection is used as-is; it may be created on first insert.

Once env (and optionally settings) and model are set, you can perform any ([database operations](database-functions.md)).
