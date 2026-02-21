# API reference — ORM

## Database (base class)

- **connect()** — Ensures the DB connection is initialized (called internally).
- **setTable(name)** — Sets the table/collection name for all CRUD operations. Call in model **__init__** with **self.tablename**.
- **initDbms()** — Chooses the engine (MySQL or Mongo) from **getenv("DB_TYPE")** and initializes the connector. Called internally.

## CRUD

- **create(data, params=None)** — Insert one (dict) or many (list of dicts). Returns Json with **data** (e.g. lastInsertedId), **status**, **message**.
- **read(filters=None, params=None)** — Query. **filters**: dict; **params**: limit, skip, sort, sortfield, include, exclude, bool. Returns **data** (one dict or list), **status**, **message**.
- **update(filters=None, data=None, params=None)** — Update one or many. **data** must be a dict. **params.limit**: 0 = all matching. Returns **status**, **message** (data often None).
- **delete(filters, params=None)** — Delete one or many. **params.limit**: 0 = all matching. Returns **status**, **message**.

All methods are **async**. Filter and param shape are the same for MySQL and Mongo; see [Filters and params](../database/filters-and-params.md).
