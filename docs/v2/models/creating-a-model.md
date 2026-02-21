# Creating a model

Models are the data layer: they provide direct access to database tables using core functions **create**, **read**, **update**, **delete**, and other helper database operations through a single API for ([supported Database systems](../getting-started/supported-databases.md))

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
