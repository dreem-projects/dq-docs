# Read

Use **read(filters, params)** to query the table or collection. The same API works for MySQL and Mongo.

## Signature

```python
result = await model.read(filters=None, params=None)
```

- **filters** — Dict of conditions (see [Filters and params](filters-and-params.md)). Omit or pass `None` to read without a WHERE clause (subject to params).
- **params** — Optional dict: `limit`, `skip`, `sort`, `sortfield`, `include`, `exclude`, `bool`.

## Single vs multiple rows

- **Single document/row** — Pass `params={'limit': 1}`. The ORM returns `data` as a single dict (or `None` if no record).
- **Multiple** — Omit `limit` or use `limit` > 1 (e.g. `params={'limit': 10}`). The ORM returns `data` as a list of dicts.
- **No limit** — For MySQL, `limit` 0 may mean no limit (implementation-specific). Check the engine docs; typically you pass a large `limit` or rely on default.

## Return shape

The method returns a framework **Json** object (or similar) with:

- **data** — One dict, a list of dicts, or `None` (e.g. when status is NO_RECORD).
- **status** — e.g. `SysCodes.READ_SUCCESS` or `SysCodes.NO_RECORD`, `READ_FAILED`.
- **message** — e.g. `SysMessages.READ_SUCCESS`.

Example in a controller:

```python
mod = SampleModel()
out = await mod.read(filters={'id': id}, params={'limit': 1})
# out.data, out.status, out.message
```

With params for list + sort + projection:

```python
out = await mod.read(
    params={
        'include': ['name', 'email'],
        'limit': 5,
        'skip': 10,
        'sort': 1,
        'sortfield': 'name'
    }
)
```

See [Filters and params](filters-and-params.md) for filter shape and param details.
