# Multiple routes and route groups

You can split routes across several view modules and use **route groups** to apply a common prefix (or postfix) to a set of routes.

## Multiple view files

Create as many view modules as you need (e.g. `views/endpoints.py`, `views/admin.py`, `views/api_v1.py`). Each exports a `routes` list. At startup, merge them and pass to the Dispatcher:

```python
from views.endpoints import routes as endpoints_routes
from views.admin import routes as admin_routes
from views.api_v1 import routes as api_routes

all_routes = endpoints_routes + admin_routes + api_routes
Dispatcher.initRoutes(all_routes)
```

## Route groups

Use **routegroup(prefix, postfix)** to apply a prefix (and optionally postfix) to a list of routes:

```python
from dreema.routing import route, routegroup
import controllers.sampleController as Sample

api_routes = routegroup(prefix="/api/v1", postfix="")(
    [
        route(path="/sample-create", methods=["POST"], handler=Sample.SampleController.sampleCreate),
        route(path="/sample-read", methods=["GET"], handler=Sample.SampleController.sampleRead),
    ]
)
# Resulting paths: POST /api/v1/sample-create, GET /api/v1/sample-read
```

Combine ungrouped and grouped lists:

```python
single = [
    route(path="/", methods=["GET"], handler=Sample.SampleController.welcome),
]
routes = single + api_routes
```

See [Route groups](../routing/route-groups.md) for full details.
