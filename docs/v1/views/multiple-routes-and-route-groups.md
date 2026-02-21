# Multiple routes and route groups

You can split routes across view modules and use **route groups** to keep related routes in a class and apply a common **prefix** (and optional postfix) when you register them in the main endpoints file.

---

## Route groups: define a class, then register

Route grouping has two steps: **define** a class with a `route` list, then **register** that list in your main view entry (e.g. `views/endpoints.py`) using **routegroup(routes, prefix="...")**.

### Step 1 — Create a class and define routes

Create a class whose **`route`** attribute is a list of **route(path, methods, handler)**. Paths here are relative (e.g. `"/login"`, `"/list"`). No prefix yet; the class only groups the definitions.

Example: `views/users/authview.py`

```python
from dreema.routing import route
from controllers.userController import UserController

class UserAuthView:
    route = [
        route(path="/login", methods=["POST"], handler=UserController.login),
        route(path="/logout", methods=["POST"], handler=UserController.logout),
        route(path="/me", methods=["GET"], handler=UserController.getMe),
        route(path="/update", methods=["POST"], handler=UserController.update),
    ]
```

You can have multiple such classes in the same or different files (e.g. `ProductView`, `OrderView`).

### Step 2 — Register in endpoints

In your **main view entry** (e.g. `views/endpoints.py`), import the route list and **register** it by adding **routegroup(routes, prefix="...")** to the main **routes** list. **routegroup(routes_list, prefix="", postfix="")** takes the list first, then optional prefix and postfix; it returns a list of routes with the prefix/postfix applied to each path.

Example: `views/endpoints.py`

```python
from dreema.routing import route, routegroup
from .api.endpoints import _ALL_ROUTES as API_ROUTES
from .products.endpoints import _ALL_ROUTES as PRODUCT_ROUTES
import controllers.appController as app

routes = [
    # Single routes
    route(path="/", methods=["GET", "POST"], handler=app.AppController.welcome),

    # Register route groups with a prefix
    routegroup(UserAuthView, prefix="api"),

    routegroup(ProductView, prefix="products"),
]
```

The Dispatcher flattens the main **routes** list (single route objects and lists returned by **routegroup**), so both styles can be mixed.

---

## Summary

| Step                     | Where                                                            | What you do                                                                                          |
| ------------------------ | ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Define a group           | View module (e.g. `authview.py`, `productview.py`)               | Create a class with a **route** list of **route(path, methods, handler)**.                           |
| Optional: combine groups | Sub-endpoints (e.g. `api/endpoints.py`, `products/endpoints.py`) | Build **\_ALL_ROUTES** with **\*routegroup(SomeView.route)** or **\*routegroup(..., prefix="...")**. |
| Register                 | Main endpoints (e.g. `views/endpoints.py`)                       | Add **routegroup(IMPORTED_ROUTES, prefix="...")** to the main **routes** list.                       |

**routegroup** signature: **routegroup(routes_list, prefix="", postfix="")** — list first, then optional prefix and postfix.

See [Defining routes in views](defining-routes-in-views.md) for **route(path, methods, handler)** and [Creating views](creating-views.md) for where view files live.
