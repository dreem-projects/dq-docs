# Delete

Use **delete(filters, params)** to remove rows or documents. The same filter shape works for MySQL and Mongo.

## Signature

```python
result = await model.delete(filters, params=None)
```

- **filters** — Dict of conditions (see [Filters and params](filters-and-params.md)). Required for single-record delete; for “delete all” the implementation may require `params={'limit': 0}` and possibly empty or no filters (check engine behavior).
- **params** — Optional. **limit**: `0` can mean “delete all matching” (bulk delete); non-zero may limit the number of deletions (e.g. 1 = delete one).

## Delete one

```python
result = await model.delete(filters={'id': id})
```

## Delete many

To delete all matching records, use **params={'limit': 0}** (and filters as needed):

```python
result = await model.delete(filters={'flag': 0}, params={'limit': 0})
```

If filters are omitted and bulk delete is allowed, behavior is implementation-specific (e.g. MySQL may allow `delete from table` when `limit` is 0 and filters are None).

## Return shape

- **data** — Often `None`.
- **status** — e.g. `SysCodes.DELETE_SUCCESS` or `DELETE_FAILED`.
- **message** — e.g. `SysMessages.DELETE_SUCCESS`.

Invalid input (e.g. filters not a dict when limit ≠ 0) returns an error (e.g. "delete required filter(s). For bulk delete, set limit to 0").
