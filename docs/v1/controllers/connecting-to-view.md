# Connecting to view

After [creating](creating-controllers.md) a controller and defining handlers in [how to use](using-controllers.md), connect them to the app by **wiring routes in a view**. The Dispatcher calls the handler when the route matches.

## Wire routes in a view

Register each controller handler in a view so the route points to the correct async method:

```python
import controllers.sampleController as Sample

routes = [
    route(path="/", methods=["GET"], handler=Sample.SampleController.welcome),
    route(path="/sample-create", methods=["POST"], handler=Sample.SampleController.sampleCreate),
    route(path="/sample-read", methods=["GET"], handler=Sample.SampleController.sampleRead),
]
```

The **handler** is the controller’s async method (e.g. `Sample.SampleController.sampleCreate`). Use the same import path and class name as in your controller module.

## Multiple handlers and HTTP methods

Define one route per path + method combination. Different handlers for list vs create:

```python
routes = [
    route(path="/items", methods=["GET"], handler=Sample.SampleController.listItems),
    route(path="/items", methods=["POST"], handler=Sample.SampleController.createItem),
]
```

## Route groups

To group controller routes under a prefix (e.g. `/api/sample/...`), use [Route groups](../routing/route-groups.md). Import your controller and attach the same handlers under the group prefix.

## Summary

1. Create the controller file (see [Creating controllers](creating-controllers.md)).
2. Implement handlers and database logic (see [How to use](using-controllers.md)).
3. Import the controller in the view and add **route(path, methods, handler=Controller.method)** for each endpoint.
