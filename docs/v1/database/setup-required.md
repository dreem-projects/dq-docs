# Database setup required

Before working with the ORM, ensure you have a proper environment configuration and that your model specifies the table or collection name. If a `.env` file does not exist in your project’s root directory, either create a new one named `.env` or rename the provided `.env.sample` file accordingly.

## Environment variables

In `.env` (from `.env.template`):

| Variable      | Description                                                                         | Example                                                                                              |
| ------------- | ----------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `DB_TYPE`     | Engine: see [Supported database systems](../getting-started/supported-databases.md) | e.g. `mongo`                                                                                         |
| `DB_HOST`     | Database host                                                                       | `localhost`                                                                                          |
| `DB_PORT`     | Port                                                                                | e.g. `27017` or `3306` (see [Supported database systems](../getting-started/supported-databases.md)) |
| `DB_NAME`     | Database name                                                                       | `mydb`                                                                                               |
| `DB_USER`     | User                                                                                | `dbuser`                                                                                             |
| `DB_PASSWORD` | Password                                                                            | `secret`                                                                                             |
| `DB_USE_TLS`  | Optional; use TLS for connection                                                    | `True`                                                                                               |

The ORM uses these in **context.py** at startup: `AppContext.init()` connects the database using `DB_TYPE` to choose the engine ([supported database systems](../getting-started/supported-databases.md)).

## Model: setTable(tablename)

Each model must call **setTable(self.tablename)** in `__init__` so all ([database operations](../database/database-functions.md)) operations target the correct table or collection:

```python
class SampleModel(database.Database):
    tablename = 'dquode_sample'

    def __init__(self):
        super().__init__()
        self.setTable(self.tablename)
```

- For SQL backends (see [Supported database systems](../getting-started/supported-databases.md)) — The table must exist; DQuode does not create tables or run migrations.
- For document backends — The collection is used as-is; it may be created on first insert.

Once env and model are set, you can perform any ([database operations](../database/database-functions.md)).
