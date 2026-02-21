# Route Groups

When you have several routes sharing a common path functionality (a prefix or postfix), **route groups** let you avoid repetitive code as well as keeping your route organized.

You define route groups in one place (e.g. per-area views and endpoints), then **register them all in `views/endpoints.py`**. The Dispatcher only loads the `routes` list from that file.

---

## 1. Define route groups somewhere

Define routes in view classes and collect them in an area’s `endpoints` module. Use `routegroup` to apply a prefix (or postfix) to each group.

**Example: view with a list of routes**

```python title="views/sampleview.py"
from dreema.routing import route

class SampleView:
    route = [
        route(path="/list", methods=["GET"], handler=SampleController.list),
        route(path="/create", methods=["POST"], handler=SampleController.create),
    ]
```

**Example: area endpoints that build route groups**

Paths are built as: `prefix + path + postfix`.

---

## 2. Register them in `views/endpoints.py`

Import each area’s route list and add it to the main `routes` list using `routegroup`. Only routes registered here are active.

```python title="views/endpoints.py"
from dreema.routing import route, routegroup
from .view.sampleview import route

routes = [
    # Single routes
    route(path="/", methods=["GET"], handler=Handler),

    # Route groups:
    routegroup(route, prefix="sample/"),
]
```

The Dispatcher reads `routes` from this file at startup. So:

1. **Define** route groups in view files and area `endpoints.py` (e.g. `views/student/endpoints.py`).
2. **Register** all of them in `views/endpoints.py` by adding each group to the `routes` list.

> See [all about routes, groups in views here](../views/creating-views.md)
