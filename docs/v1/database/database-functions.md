# Database functions

The ORM provides the following functions which are consistent across all [supported databases](../getting-started/supported-databases.md).

| Function                          | Purpose                                      |
| --------------------------------- | -------------------------------------------- |
| **create(data, params)**          | Insert one or many rows/documents            |
| **read(filters, params)**         | Query the table or collection                |
| **update(filters, data, params)** | Update one or many rows/documents            |
| **delete(filters, params)**       | Remove rows or documents                     |
| **count(filters, params)**        | Return the number of matching rows/documents |

Filter and param shape is shared across backends; see [Filters and params](filters-and-params.md).

---

## Create

```python
result = await model.create(data, params=None)
```

- **data** — A **dict** (single insert) or a **list of dicts** (bulk insert).
- **params** — Optional;

Returns a result with **data** (e.g. last inserted id), **status**, **message**. Single insert: pass one dict. Bulk: pass a list of dicts.

Details: [Create](create.md).

---

## Read

```python
result = await model.read(filters=None, params=None)
```

- **filters** — Dict of conditions. Omit or `None` to read without a WHERE clause.
- **params** — Optional parameters such as : `limit`, `skip`, etc.
  [Learn more about filters and parameters here.](filters-and-params.md)

  Returns **data** (often `None`), **status**, **message**.

  Details: [Read](read.md).

---

## Update

```python
result = await model.update(filters=None, data=None, params=None)
```

- **filters** — Dicionary of conditions
- **data** — New data to be updated.
- **params** — Optional parameters such as : `limit`, `skip`, etc.
  [Learn more about filters and parameters here.](filters-and-params.md)

  Returns **data** (often `None`), **status**, **message**.

Details: [Update](update.md).

---

## Delete

```python
result = await model.delete(filters, params=None)
```

- **filters** — Dicionary of conditions
- **params** — Optional parameters such as : `limit`, `skip`, etc.
  [Learn more about filters and parameters here.](filters-and-params.md)

  Returns **data** (often `None`), **status**, **message**.

Details: [Delete](delete.md).

---

## Count

```python
result = await model.count(filters=None, params=None)
```

- **filters** — Dicionary of conditions
- **params** — Optional parameters such as : `limit`, `skip`, etc.
  [Learn more about filters and parameters here.](filters-and-params.md)

  Returns **data** (often `None`), **status**, **message**.

Details: [Count](count.md).
