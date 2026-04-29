# Creating a model

Models are the data layer: they provide direct access to database tables using core functions **create**, **read**, **update**, **delete**, and other helper([database operations](../database/database-functions.md)) through a single API for ([supported Database systems](../getting-started/supported-databases.md))

Create the model file in one of two ways:

### Option A: From the terminal

From the project root, run:

```bash
python terminal.py create=model class=SampleModel name=sampleModel
```

- **create** — Must be `model` for a model file.
- **class** — The model class name (e.g. `SampleModel`, `GradesModel`).
- **name** — The file name without `.py` (e.g. `sampleModel`).

This creates `models/sampleModel.py`. Change the `__table_name__` to your actual table name. You are set and ready.

### Option B: Copy an existing model

1. Copy an existing model file (e.g. `models/student/gradeModel.py`) to a new path (e.g. `models/student/myModel.py`).
2. Open the new file and change the **class name** and **tablename** to match your table/collection:

```python
class MyModel(database.Database):
    tablename = 'my_table'   # your table or collection name
    # ...
```

If the project uses a central model list (e.g. `models/_modelsList.py`), register the new model there so it can be used by DBware or other code that looks up models by key.

---

## Choosing the database connection

When you use [multiple databases](../database/setup-required.md#multi-database), the model must accept a **connection** argument and pass it to `super().__init__(connection)`. The connection name must match a key under `databases` in [settings.json](../guides/config.md#settings-non-secrets-settingsjson) (e.g. `default`, `app`, `customers`). Credentials for each connection live in [.env](../guides/config.md#secrets-env).

Example:

```python
class SampleModel(database.Database):
    tablename = 'Dreema_sample'

    def __init__(self, connection='default'):
        super().__init__(connection)
        self.setTable(self.tablename)
```

- **connection='default'** — Uses the default database (from .env or from `databases.default` in settings + `DB_USER`/`DB_PASSWORD` in .env).
- **connection='app'** — Uses the **app** database (from `databases.app` in settings + `DB_APP_USER`/`DB_APP_PASSWORD` in .env).

You can also call **setConnection(name)** on an existing model instance to switch the connection for subsequent operations.

---
