# Creating views

Views are **route definitions**: they build the path–method–handler list used by the Dispatcher. They do not render HTML; they map URLs and HTTP methods to controller handlers. View modules live under the **views/** package, and the framework loads a **routes** list from them (e.g. `from views.endpoints import routes`).

!!! tip "Quick path"
Create a view in two ways: **scaffold from the terminal** (via the dquode terminal) or **copy an existing view file**. Both end with a new file under `views/` ready for you to wire routes to controllers.

---

## Option A — Scaffold from the terminal

From the project root (where `terminal.py` lives in dquode):

```bash
python terminal.py create=view class=Endpoints name=endpoints
```

| Argument   | Meaning                                                          |
| ---------- | ---------------------------------------------------------------- |
| **create** | Must be `view` for a view file.                                  |
| **class**  | The view class or module name (e.g. `Endpoints`, `AdminRoutes`). |
| **name**   | The file name without `.py` (e.g. `endpoints`, `admin`).         |

This creates `views/<name>.py` with a stub that imports `route` (and optionally `routegroup`) and defines a route list. Open the file and wire **path**, **methods**, and **handler** to your controllers. Ensure the module exports a **routes** list so the Dispatcher can load it (e.g. in dquode, `from views.endpoints import routes`).

---

## Option B — Copy an existing view

1. Copy an existing view file (e.g. `views/endpoints.py`) to a new path (e.g. `views/admin.py`).
2. Open the new file and update the **imports** (controller modules) and the **routes** list: change paths, methods, and handlers to match your controllers.
3. If your app merges multiple view modules, register the new view’s routes in the place where the Dispatcher gets its route list (e.g. the main `views/endpoints.py` or a central routing module).

!!! info "Reusing structure"
Copying is handy when you already have a view with the right structure (e.g. routegroup, shared prefix) and want a similar one for another area of the app.

---

## Where views live

Keep every view module under your **views** package:

- Flat: `views/endpoints.py`, `views/admin.py`
- Nested: `views/api/endpoints.py`, `views/admin/dashboard.py`

---
