# Creating controllers

Controllers are the **request handlers** of your app: async functions that receive the **Request**, run your **business logic**, and return a **Response**. They live in the `controllers/` package and are wired to routes in views.

!!! tip "Quick path"
    Create a controller in two ways: **scaffold from the terminal** or **copy an existing file**. Both end with a new file under `controllers/` ready for you to edit.

---

## Option A — Scaffold from the terminal

From the project root:

```bash
python terminal.py create=controller class=SampleController name=sampleController
```

| Argument | Meaning |
|----------|--------|
| **create** | Must be `controller` for a controller file. |
| **class** | The controller class name (e.g. `SampleController`, `AuthController`). |
| **name** | The file name without `.py` (e.g. `sampleController`). |

This creates `controllers/sampleController.py` with a stub class and a sample async handler. Open the file and adapt the class and handlers to your routes and logic.

---

## Option B — Copy an existing controller

1. Copy an existing controller (e.g. `controllers/school/authController.py`) to a new path (e.g. `controllers/school/myController.py`).
2. Open the new file and update the **class name** and **handler names**. Adjust imports (models, utilities) as needed.
3. Wire the new handlers in your views — see [Connecting to view](connecting-to-view.md).

!!! info "Reusing structure"
    Copying is handy when you already have a controller with the right structure (e.g. auth, validation) and want a similar one for another resource.

---

## Where controllers live

Keep every controller under your **controllers** package:

- Flat: `controllers/sampleController.py`
- Nested: `controllers/student/gradeController.py`

Views import these modules when defining routes. Stick to one controller per file and a clear naming convention (e.g. `*Controller.py`) so the codebase stays easy to navigate.

---

**Next:** [How to use](using-controllers.md) controllers (imports, handlers, database operations, responses) · [Connecting to view](connecting-to-view.md) (wiring routes).
