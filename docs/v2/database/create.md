# Create

Use **create(data, params)** to insert one or many rows/documents. The same API works for MySQL and Mongo.

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

- **data** — e.g. `{"lastInsertedId": id}` (MySQL) or Mongo equivalent (e.g. `_id`).
- **status** — e.g. `SysCodes.CREATE_SUCCESS`.
- **message** — e.g. `SysMessages.CREATE_SUCCESS`.

If **autoCreateColumns** is defined on the model, those fields are merged into each inserted document/row before insert.

## Bulk insert

Pass a **list of dicts**:

```python
result = await model.create([
    {"name": "A", "value": 1},
    {"name": "B", "value": 2},
])
```

Return shape is similar; `lastInsertedId` (or equivalent) may refer to the last inserted id. Validation: `data` must be a dict or a list; otherwise the ORM returns an error (e.g. DB_CONNECTION_FAILED with message "data must be a dictionary or a list for bulk create").
