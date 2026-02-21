# Filters and params

The ORM uses the **same filter and param shape** for all [supported database systems](../getting-started/supported-databases.md). Use this page as the single reference for all CRUD operations.

## Filter shape

**filters** is a dict. Each key is a **field name**; each value is either:

1. **Plain value** — Treated as equality: `{"name": "Alice"}` → `name = 'Alice'`.
2. **Condition dict** — Use **op** and **value** (and optionally **bool**):
   - **value** — The value to compare.
   - **op** — Operator: `=`, `!=`, `>`, `<`, `<=`, `>=`. Document backends also support `in`, `nin` (see [Supported database systems](../getting-started/supported-databases.md)).
   - **bool** — How to combine with the next condition: `and` or `or` (default `and`).

Examples:

```python
# Equality
filters={'id': 1}
filters={'name': 'Alice'}

# With operator
filters={'age': {'op': '>', 'value': 18}}
filters={'status': {'op': '!=', 'value': 0}}
filters={'role': {'op': 'in', 'value': ['admin', 'user']}}
```

## Multiple conditions on the same field

Python dicts cannot have duplicate keys. For **multiple conditions on the same field** (e.g. `age > 10 AND age < 100`), use a **list of condition dicts** for that field. The exact format is implementation-dependent; a common pattern is:

```python
# Example pattern (check implementation): list of {op, value} for same field
filters={'age': [
    {'op': '>', 'value': 10},
    {'op': '<', 'value': 100}
]}
```

If the engine does not support a list for one key, split into separate fields or use raw queries. See [Limitations — Multiple conditions same field](../limitations/index.md).

## Params (read / delete / update)

| Param         | Use                                                                                                                                                   | Example                                 |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| **limit**     | Max number of rows/documents (read: result set; delete/update: how many to affect). `0` can mean “no limit” (read) or “all matching” (delete/update). | `params={'limit': 5}` or `{'limit': 0}` |
| **skip**      | Number to skip (read).                                                                                                                                | `params={'skip': 10}`                   |
| **sort**      | Sort direction: `1` ascending, `-1` descending.                                                                                                       | `params={'sort': 1}`                    |
| **sortfield** | Field name for sort.                                                                                                                                  | `params={'sortfield': 'name'}`          |
| **include**   | Fields to return (projection).                                                                                                                        | `params={'include': ['name', 'email']}` |
| **exclude**   | Fields to omit (projection). If both include and exclude are used, behavior is engine-specific (e.g. last wins).                                      | `params={'exclude': ['password']}`      |
| **bool**      | How to combine filter conditions: `and` or `or`.                                                                                                      | `params={'bool': 'or'}`                 |

Example:

```python
await model.read(
    filters={'flag': 1},
    params={'include': ['name'], 'limit': 5, 'skip': 10, 'sort': 1, 'sortfield': 'name'}
)
```
