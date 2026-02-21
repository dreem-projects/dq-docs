# Extensibility

You can extend status codes and messages so the backend–frontend contract stays consistent while allowing custom codes.

## extendCodes

In **registers.py**:

```python
from dreema.helpers import extendCodes

extendCodes({
    200: "CUSTOM_SUCCESS",
    400: "CUSTOM_VALIDATION_ERROR",
})
```

This merges or overrides the default **SysCodes** (or equivalent). Use the same codes in **response(status=...)** so clients can rely on a fixed set of values.

## extendMessages

```python
from dreema.helpers import extendMessages

extendMessages({
    200: "Operation completed",
    400: "Invalid input",
})
```

Maps code to a default message. The framework may use these when you don’t pass an explicit **message** in **response(...)**.
