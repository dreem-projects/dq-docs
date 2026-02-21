# Validation

Request validation is explicit: use **applyRules** or **trimApplyRules** on the Request in the controller.

## applyRules

**request.applyRules(rules [, source])** — Validates the given **rules** against a source (default: request body). Returns a result object with **status**, **data**, **message**. If validation fails, **status** is negative.

## trimApplyRules

**request.trimApplyRules(rules [, source])** — Same as applyRules but **trims** the source to only the keys defined in **rules**; unknown keys are dropped. Useful to avoid passing extra fields to the model. **source** can be the body or **request.params()** for query params.

Example:

```python
body = await request.trimApplyRules({
    'id': 'required',
    'name': 'required',
}, request.params())  # validate query params

if body.status < 0:
    return response(body, custom=True)

# body.data has only id, name (trimmed)
```

## Rule names

Common rules include: **required**, **int**, **str**, **float**, **list**, **bool**, **nullable**. Exact names and options depend on the dreema validation implementation.

## Return value

The validation result has:

- **status** — Positive on success, negative on failure.
- **data** — Parsed/trimmed data (e.g. object with attributes or dict).
- **message** — Error message when status < 0.

Use **response(result, custom=True)** to return the validation result as the response body when validation fails.
