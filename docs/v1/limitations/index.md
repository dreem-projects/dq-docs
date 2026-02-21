# Limitations

This section describes current constraints and behaviors you should be aware of when building with DQuode.

## Multiple conditions on the same field

Python dicts cannot have duplicate keys. So you cannot express “age > 10 and age < 100” with two separate keys `age`. Use a **list of condition dicts** for that field:

```python
filters={'age': [
    {'op': '>', 'value': 10},
    {'op': '<', 'value': 100}
]}
```

Support for this list shape may vary by engine; if not supported, use a single condition or engine-specific options. See [Filters and params](../database/filters-and-params.md).

## Full structure replacement

**Update** behaves as “set these fields” (partial update); see [Supported database systems](../getting-started/supported-databases.md). It does **not** replace the entire row/document in one call. To replace the full structure, you can:

- Call **update** with all current fields, or
- **Delete** and then **create** (if acceptable for your consistency model).

Exact semantics (e.g. default values, missing fields) are engine-dependent.

## Server restart

Route cache and DB connections are built at **startup** (lifespan). Changing route definitions or env (e.g. DB_TYPE) requires a **server restart** (or process reload in development). There is no hot-reload of routes only.

## No migrations; DB constraints external

DQuode does **not** run or ship migrations. Tables and collections must be created and altered outside the app (manual SQL, migration tools, or the database shell). See [Supported database systems](../getting-started/supported-databases.md). Constraints (unique, foreign key, indexes) are managed in the database; the ORM does not enforce them beyond what the engine does.
