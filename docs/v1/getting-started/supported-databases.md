# Supported database systems

DQuode's built-in ORM supports the following databases through a single API. Choose the backend with the `DB_TYPE` setting in [Setup](setup.md); the same model and CRUD calls work for both.

## MySQL

- **Driver:** `aiomysql` (async).
- **`.env`:** Set `DB_TYPE=mysql` and the usual `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` (and optionally `DB_USE_TLS`).
- **Use case:** Relational data, existing MySQL infrastructure.

## MongoDB

- **Driver:** `motor` with `pymongo` (async).
- **`.env`:** Set `DB_TYPE=mongo` and `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` (and optionally `DB_USE_TLS`).
- **Use case:** Document storage, flexible schema.

!!! info "One API for both"
    Models extend `database.Database` and use the same methods regardless of backend:

    - `create(data, params=None)`
    - `read(filters=None, params=None)`
    - `update(filters=None, data=None, params=None)`
    - `delete(filters, params=None)`

    Filters and params use the same shape for all supported backends; see [Filters and params](../database/filters-and-params.md). You only need a running database and the correct `.env` when you use routes that call the ORM.
