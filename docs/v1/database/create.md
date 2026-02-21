# Create

Use **create(data, params)** to insert one or many rows/documents. The same API works for all [supported database systems](../getting-started/supported-databases.md).

## Signature

```python
result = await model.create(data, params=None)
```

- **data** — A **dict** (single insert) or a **list of dicts** (bulk insert).
- **params** — Optional; engine-specific options if supported.

## Single insert

```python
body = await request.body()
result = await model.create(body.data)
```

The ORM returns a result object with:

- **data** — e.g. `{"lastInsertedId": id}` or backend-specific equivalent (e.g. `_id`); see [Supported database systems](../getting-started/supported-databases.md).
- **status** — e.g. `SysCodes.CREATE_SUCCESS`.
- **message** — e.g. `SysMessages.CREATE_SUCCESS`.

## Bulk insert

Pass a **list of dicts**:

```python
result = await model.create([
    {"name": "A", "value": 1},
    {"name": "B", "value": 2},
])
```

Return shape is similar; `lastInsertedId` as a list.
