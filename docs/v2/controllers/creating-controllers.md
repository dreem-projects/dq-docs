# Creating controllers

Controllers are **request handlers**: async functions that receive the **Request**, proceses **business logic**, and returns a **Response**. They live in the `controllers/` package and are referenced by routes in views.

## Creation process

Create the controller file in one of two ways:

### Option A: From the terminal

From the project root, run:

```bash
python terminal.py create=controller class=SampleController name=sampleController
```

- **create** — Must be `controller` for a controller file.
- **class** — The controller class name (e.g. `SampleController`, `AuthController`).
- **name** — The file name without `.py` (e.g. `sampleController`).

This creates `controllers/sampleController.py` with a stub class and a sample async handler that uses `request.applyRules` and `response()`. Edit the class and handlers to match your routes and logic.

### Option B: Copy an existing controller

1. Copy an existing controller file (e.g. `controllers/school/authController.py`) to a new path (e.g. `controllers/school/myController.py`).
2. Open the new file and change the **class name** and **handler names** to match your use case. Update imports (e.g. models, utilities) as needed.
3. Wire the new handlers in your views (see [Wire routes in a view](#4-wire-routes-in-a-view) below).

---

## 1. Imports

In the new controller module, import what you need:

```python title="controllers/sampleController.py"
from dreema.requests import Request
from dreema.responses import response
from dreema.responses import SysCodes, SysMessages
from models.sampleModel import SampleModel
```

- **Request** — Type hint for the first argument of each handler.
- **response** — Helper to build the unified response envelope.
- **SysCodes** / **SysMessages** — Standard status codes and messages.
- **Models** — For database operations.

## 2. Define a class with async handlers

Use a class and define **async** methods that take `request: Request`:

```python
class SampleController:

    async def welcome(request: Request):
        return response(message="Setup completed", status=SysCodes.SETUP_COMPLETED)

    async def sampleCreate(request: Request):
        body = await request.body()
        mod = SampleModel()
        res = await mod.create(data=body.data)
        return response(res, custom=True)

    async def sampleRead(request: Request):
        mod = SampleModel()
        res = await mod.read(params={'limit': 5})
        return response(res, custom=True)
```

Each handler can use `request.body()`, `request.params()`, `request.applyRules(...)` or `request.trimApplyRules(...)`, and your models. Return `response(...)` (or a result passed to `response(..., custom=True)`).

## 3. Place the file

Keep the controller under your **controllers** package (e.g. `controllers/sampleController.py` or `controllers/student/sampleController.py`). Import it from views when defining routes.

## 4. Wire routes in a view

Register each handler in a view so the Dispatcher can call it when the route matches:

```python
import controllers.sampleController as Sample

routes = [
    route(path="/", methods=["GET"], handler=Sample.SampleController.welcome),
    route(path="/sample-create", methods=["POST"], handler=Sample.SampleController.sampleCreate),
    route(path="/sample-read", methods=["GET"], handler=Sample.SampleController.sampleRead),
]
```

The **handler** is the controller’s async method. See [Route groups](../routing/route-groups.md) if you group routes under a prefix.
