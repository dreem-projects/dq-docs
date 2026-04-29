# 📂 Dreema Project Structure

A typical **Dreema** project (such as `Dreema/`) is organized clearly and predictably:

```plaintext
Dreema/
├── index.py                # ASGI application entrypoint: handles startup, requests, and shutdown
├── start.py                # Runs uvicorn (server); sets port and reload behavior from environment
├── context.py              # Application context: database, Redis (optional), and route initialization
├── .env.template           # Example environment variables
├── .env                    # Your local environment (never committed)
├── requirements.txt        # Python dependencies
├── registers.template.py   # Template for authentication and code/message extension
├── registers.py            # Optional: custom auth and codes/messages
├── config.py               # App-wide CONFIG and utility getconfig (optional)
├── controllers/            # Business logic: one file per logical area
│   └── sampleController.py
├── models/                 # Data layer: ORM model definitions
│   └── sampleModel.py
└── views/                  # Route declarations (endpoints)
    └── endpoints.py        # Exports route list to Dispatcher
```

---

## 🧩 Key Components & Roles

| Component          | Purpose                                                                                                                                                                                                   |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`index.py`**     | Main ASGI application. Creates `Request` objects, dispatches via `Dispatcher.dispatchRoute()`, returns unified responses.<br>Runs `AppContext.init()` and `AppContext.shutdown()` during lifespan events. |
| **`start.py`**     | Launches the server with environment-driven port and reload handling.                                                                                                                                     |
| **`context.py`**   | Initializes key resources: database connection, Redis (optional), and pre-caches routes via `Dispatcher.initRoutes()`.                                                                                    |
| **`controllers/`** | Async request handlers grouped by concern (e.g., `sampleController.py`). Each defines functions that process requests and return responses.                                                               |
| **`models/`**      | ORM models for each table/collection. Each defines `tablename` and uses the standard ORM API (`create`, `read`, `update`, `delete`).                                                                      |
| **`views/`**       | Define endpoints: one or many files exporting route lists using `route()`/`routegroup()` patterns; passed to the dispatcher at init.                                                                      |

---

You’re free to scale by adding more controller, model, or view files as needed. Combine their route lists in `context.py` when calling `initRoutes()` to fully define your API’s landscape.
