# Update

Use **update(filters, data, params)** to update one or many rows/documents. The same API works for all [supported database systems](../getting-started/supported-databases.md).

## Signature

```python
result = await model.update(filters=None, data=None, params=None)
```

- **filters** — Dict of conditions (see [Filters and params](filters-and-params.md)).
- **data** — Dict of field names and new values. **Must be a dict**; the ORM applies this as the update payload (backend-specific; see [Supported database systems](../getting-started/supported-databases.md)).
- **params** — Optional. **limit**: `0` can mean “update all matching”; non-zero (e.g. 1) updates one.

## Update one

```python
result = await model.update(
    filters={'id': id},
    data={'name': name, 'status': 1}
)
```

## Update many

To update all matching records, use **params={'limit': 0}**:

```python
result = await model.update(
    filters={'flag': 1},
    data={'status': 2},
    params={'limit': 0}
)
```

## Return shape

- **data** — Often `None`.
- **status** — e.g. `SysCodes.UPDATE_SUCCESS` or `UPDATE_FAILED`.
- **message** — e.g. `SysMessages.UPDATE_SUCCESS`.

If **data** is not a dict, the ORM returns an error (e.g. "data and filter must be a dictionary"). Update semantics are engine-specific: typically “set these fields” (partial update). For full document/row replacement, see [Limitations](../limitations/index.md).
