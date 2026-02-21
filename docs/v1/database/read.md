# Read

Use **read(filters, params)** to query the table or collection. The same API works for all [supported database systems](../getting-started/supported-databases.md).

## Signature

```python
result = await model.read(filters=None, params=None)
```

- **filters** — Dict of conditions (see [Filters and params](filters-and-params.md)). Omit or pass `None` to read without a WHERE clause (subject to params).
- **params** — Optional dict: `limit`, `skip`, `sort`, `sortfield`, `include`, `exclude`, `bool`.

## Default behavior

- **read() with nothing** — `read()` (no filters, no params) returns the **last item entered** in the table or collection. **data** is a single dict (or `None` if empty).
- **read(filters) with no params** — When you pass **filters** but omit **params**, the ORM assumes **limit 1**. You get a single document/row (or `None`). **data** is a single dict.
- **Bulk read** — To get multiple or all matching records, pass **params** and set **params['limit'] = 0**. The ORM returns **data** as a list of dicts. Use `limit` > 0 (e.g. 10) to cap the number of results.

## Single vs multiple rows

- **Single document/row** — Use `read(filters)` with no params (limit 1 assumed), or `read(filters, params={'limit': 1})`. **data** is a single dict or `None`.
- **Multiple / bulk** — Pass **params={'limit': 0}** (or a positive limit like 10). **data** is a list of dicts.
- **Last item only** — Use `read()` with no filters and no params to get the most recently entered record.

## Return shape

The method returns a framework **Json** object (or similar) with:

- **data** — One dict, a list of dicts, or `None` (e.g. when status is NO_RECORD).
- **status** — e.g. `SysCodes.READ_SUCCESS` or `SysCodes.NO_RECORD`, `READ_FAILED`.
- **message** — e.g. `SysMessages.READ_SUCCESS`.

Examples in a controller:

```python
mod = SampleModel()

# Last item entered (no filters, no params)
out = await mod.read()
# out.data is one dict or None

# Single record by filter (no params → limit 1)
out = await mod.read(filters={'id': id})
# out.data is one dict or None

# Bulk: all matching (limit 0)
out = await mod.read(filters={'status': 1}, params={'limit': 0})
# out.data is a list of dicts

# Bulk with sort and projection
out = await mod.read(
    params={
        'include': ['name', 'email'],
        'limit': 0,
        'skip': 10,
        'sort': 1,
        'sortfield': 'name'
    }
)
# out.data is a list of dicts
```

See [Filters and params](filters-and-params.md) for filter shape and param details.
