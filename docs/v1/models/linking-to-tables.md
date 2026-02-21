# Linking models to tables

In DQuode, a model is tied to a **single table or collection**. This is the only actionable action you can take currently in the models class. DQuode takes care of the rest.

## Table/collection name

Set the class attribute **tablename** to the exact name used in the database:

```python
class SampleModel(database.Database):
    tablename = 'dquode_sample'

    def __init__(self):
        super().__init__()
        self.setTable(self.tablename)
```

- For SQL backends — `tablename` is the table name (e.g. `dquode_sample`). See [Supported database systems](../getting-started/supported-databases.md).
- For document backends — `tablename` is the collection name (e.g. `dquode_sample`).

The same model code works for both; the database system used is decided by your .env `DB_TYPE` in `.env`.

## One model, one table

- Each model class should map to **one** table or collection.
- For another table/collection, create another model class with a different `tablename`.

## When the table is created

!!! note "You own your schema"
DQuode does **not** currently run migrations or create tables for you. **You own your schema.** The table or collection must already exist, or be created by your own DB setup, migrations, or manual steps. Ensure the schema exists before performing database operations.

## Changing the table at runtime

- Do **not** change `tablename` or call `setTable` per request for the same model; keep one table per model class. For different tables, use different model classes.
