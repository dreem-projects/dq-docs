# Creating views

In DQuode, **views** are modules that define **routes**: they do not render HTML but instead build the list of path–method–handler mappings used by the Dispatcher.

## Where views live

Place view modules under the **views/** package (e.g. `views/endpoints.py`, `views/admin.py`).

## What a view exports

A view module should export a **routes** list (or similar name) that is a list of route objects. Each route is created with **route(path, methods, handler)** from `dreema.routing`.

Example minimal view (`views/endpoints.py`):

```python
from dreema.routing import route
import controllers.sampleController as Sample

routes = [
    route(path="/", methods=["GET", "POST"], handler=Sample.SampleController.welcome),
    route(path="/sample-create", methods=["POST"], handler=Sample.SampleController.sampleCreate),
    route(path="/sample-read", methods=["GET"], handler=Sample.SampleController.sampleRead),
]
```

## No business logic

Views only wire **path + methods → handler**. All logic (validation, DB, auth) lives in **controllers**; views are the routing map.

## Registering with the Dispatcher

The route list must be passed to **Dispatcher.initRoutes()** at startup. In `context.py` (or equivalent):

```python
from views.endpoints import routes

Dispatcher.initRoutes(routes)
```

If you have multiple view modules, merge their lists:

```python
from views.endpoints import routes as endpoints_routes
from views.admin import routes as admin_routes

Dispatcher.initRoutes(endpoints_routes + admin_routes)
```
