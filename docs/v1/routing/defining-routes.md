# Defining Routes

Routes define how HTTP **paths** and **methods** are linked to a **controller function**. In DQuode, you declare your routes [inside views](../views/creating-views.md). These are then gathered into a single list inside of `views/endpoints.py`, which the Dispatcher uses during startup.

---

!!!info "✨ The `route` Function"

To define a route, import the `route` utility from `dreema.routing`:

```python title="Defining routes"
from dreema.routing import route

route(path="/", methods=["GET", "POST"], handler=SomeController.welcome)
```

> When you have several routes sharing a common path functionality (a prefix or postfix), route groups let you avoid repetitive code as well as keeping your route organized.See [how to implement routes groups here](../views/creating-views.md)

<!-- **Parameters:**

- **`path`** — The URL path (e.g., `"/"`, `"/sample-create"`, `"/items"`). Paths must always start with a `/` unless already specified in `routegroups`.
- **`methods`** — A list of allowed HTTP methods (e.g., `["GET"]`, `["POST"]`, `["GET", "POST"]`).
- **`handler`** — An async controller function that takes a single argument (the **Request**) and returns a response (typically via `response(...)`). -->

---

## 📏 Path Formatting Guidelines

- Always start your paths with a leading slash: `"/"`, `"/items"`, `"/api/v1/users"`, etc.
- Paths are normalized (e.g., consistent handling of trailing slashes) when routes are cached by the Dispatcher.

## Path parameters

You can define **path parameters** in the route path using a colon-prefixed segment: `:name`. The Dispatcher matches the path by position and passes captured values to your handler.

**Syntax:** Use `:paramName` in the path. For example:

- `"/items/:id"` — matches `/items/1`, `/items/abc`; captures `id`.
- `"/schools/:schoolId/students/:studentId"` — captures `schoolId` and `studentId`.

**In the controller:** Read path parameters via `request.pathParam()`, which returns a dict of parameter names to string values.

```python
# Route: path="/items/:id", methods=["GET"], handler=ItemController.getOne)
async def getOne(request: Request):
    params = request.pathParam()   # e.g. {"id": "42"}
    item_id = params.get("id")
    # fetch and return item
```

Path parameters are matched after static routes; routes with parameters are tried only when no static route matches.

---

## 🚀 Registering Your Routes

The system handles the rest by passing all your `routes` bundled and cached to **`Dispatcher.initRoutes(routes)`** during app startup (for example, in `context.py`).

> **Tip:** Group and organize your routes across multiple files for clarity, then merge them before initialization.

---
