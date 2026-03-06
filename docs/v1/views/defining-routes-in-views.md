# Defining routes in views

Routes in views are built with **route(path, methods, handler)** and optionally **routegroup(prefix, postfix)**.

## Single route

```python
from dreema.routing import route
import controllers.sampleController as Sample

routes = [
    route(path="/", methods=["GET", "POST"], handler=Sample.SampleController.welcome),
    route(path="/sample-read", methods=["GET"], handler=Sample.SampleController.sampleRead),
    route(path="/items/:id", methods=["GET"], handler=ItemController.getOne),
]
```

- **path** — Must start with `/`. Use `:paramName` for path parameters (e.g. `"/items/:id"`); values are available via `request.pathParam()`. See [Defining routes — Path parameters](../routing/defining-routes.md#path-parameters).
- **methods** — List of HTTP methods: `["GET"]`, `["POST"]`, `["PUT"]`, `["DELETE"]`, or combined.
- **handler** — The controller’s async function (e.g. `SampleController.sampleRead`).

## Importing controllers

Use the controller module (or class) and pass its methods as handlers:

```python
import controllers.sampleController as Sample

# Class-based
route(path="/", methods=["GET"], handler=Sample.SampleController.welcome)

# If you had a function-based controller module:
# import controllers.itemController as Item
# route(path="/items", methods=["GET"], handler=Item.list_)
```

The Dispatcher will call `handler(request)` when the path and method match.

## One path, multiple methods

To allow GET and POST on the same path with different logic, define two routes:

```python
route(path="/items", methods=["GET"], handler=ItemController.list_),
route(path="/items", methods=["POST"], handler=ItemController.create),
```
