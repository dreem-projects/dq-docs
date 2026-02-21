# Database setup required

Before using the ORM, you need environment configuration and a model that sets the table/collection name.

## Environment variables

In `.env` (from `.env.template`):

| Variable | Description | Example |
|----------|-------------|---------|
| `DB_TYPE` | Engine: `mysql` or `mongo` | `mongo` |
| `DB_HOST` | Database host | `localhost` |
| `DB_PORT` | Port | `27017` (Mongo) or `3306` (MySQL) |
| `DB_NAME` | Database name | `mydb` |
| `DB_USER` | User | `dbuser` |
| `DB_PASSWORD` | Password | `secret` |
| `DB_USE_TLS` | Optional; use TLS for connection | `True` |

The ORM uses these in **context.py** at startup: `AppContext.init()` connects the database using `DB_TYPE` to choose the engine (MySQL or Mongo).

## Model: setTable(tablename)

Each model must call **setTable(self.tablename)** in `__init__` so all CRUD operations target the correct table or collection:

```python
class SampleModel(database.Database):
    tablename = 'dquode_sample'

    def __init__(self):
        super().__init__()
        self.setTable(self.tablename)
```

- **MySQL** — The table must exist; DQuode does not create tables or run migrations.
- **MongoDB** — The collection is used as-is; it may be created on first insert by Mongo.

Once env and model are set, you can call **create**, **read**, **update**, and **delete** from controllers.
