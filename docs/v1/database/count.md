# Count

Use **count(filters, params)** to get the number of rows or documents that match the given conditions. The same API works for all [supported database systems](../getting-started/supported-databases.md).

## Signature

```python
result = await model.count(filters=None, params=None)
```

- **filters** — Dict of conditions (see [Filters and params](filters-and-params.md)). Omit or pass `None` to count all records in the table or collection.
- **params** — Optional dict; engine-specific options (e.g. **limit** if the backend supports limiting the count scope).

## Examples

Count all records:

```python
mod = SampleModel()
out = await mod.count()
# out.data is the total count (number)
```

Count with filters:

```python
out = await mod.count(filters={'status': 1, 'active': True})
# out.data is the number of matching rows/documents
```

With params (e.g. limit 0 to mean no limit, or backend-specific):

```python
out = await mod.count(params={'limit': 0})
```

## Return shape

- **data** — The count as a number (integer).
- **status** — e.g. `SysCodes.READ_SUCCESS` or `READ_FAILED`, `NO_RECORD`.
- **message** — e.g. `SysMessages.READ_SUCCESS`.

Example in a controller:

```python
mod = SampleModel()
out = await mod.count(filters={'category': 'books'})
if out.status > 0:
    return response(data={'total': out.data}, message="Count completed", status=SysCodes.OP_COMPLETED)
return response(out, custom=True)
```

See [Filters and params](filters-and-params.md) for filter shape.
