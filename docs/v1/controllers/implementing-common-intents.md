# Implementing common intents

This page shows **what** handlers do from a business perspective: create only when valid, read one vs many, update when valid, delete with a required id. The code is the implementation of those intents.

!!! info "Returning your own response"
You can **return any value** from a handler (e.g. `return res`, or **bytes** for binary) and the framework sends it. Use **response()** when you want the **data**, **message**, **status** envelope, need **statuscode**, or need **headers** (e.g. **Content-Type** for images/PDFs with **custom=True**). See [Key aspects](key-aspects.md#shape-your-response) and [Response functions](../response/response-functions.md).

See [How to use](using-controllers.md) for the overall business-logic flow (validate → act → respond) and [Connecting to view](connecting-to-view.md) to wire these handlers to routes.

---

## Validate, then act

Business logic starts with “only proceed if input is valid.” Use [applyRules](../requests/request-functions.md#applyrulesrules--source) or [trimApplyRules](../requests/request-functions.md#trimapplyrulesrules--source) on the body or query, then branch on the result.

**Create: validate body, then insert**

```python
from dreema.requests import Request
from dreema.responses import response, SysCodes, SysMessages
from models.sampleModel import SampleModel

class SampleController:

    async def createItem(request: Request):
        body = await request.trimApplyRules({"name": "required,str", "email": "required,str"})
        if body.status < 0:
            return response(body, custom=True)   # validation failed — stop here

        mod = SampleModel()
        res = await mod.create(data=body.data)
        return response(res, custom=True)
```

Here the **business rule** is: “We only create when `name` and `email` are present and correctly typed.” Validation enforces that; the rest of the handler runs only when it passes.

**Read by id: validate query params, then read**

```python title="Same idea for query params"
async def getItem(request: Request):
    body = await request.trimApplyRules({"id": "required,int"}, request.queryParam())
    if body.status < 0:
        return response(body, custom=True)

    mod = SampleModel()
    res = await mod.read(filters={"id": body.data.id}, params={"limit": 1})
    return response(res, custom=True)
```

---

## Create only when the payload is valid

**Rule:** “Accept a single item or a list; validate required fields; then insert.”

```python
async def createItem(request: Request):
    body = await request.body()
    validated = await request.trimApplyRules({"name": "required,str", "value": "required,int"}, body.data if body.data else {})
    if validated.status < 0:
        return response(validated, custom=True)

    mod = SampleModel()
    res = await mod.create(data=validated.data)
    return response(res, custom=True)
```

You can extend the rule (e.g. “check name is unique”) by reading the model first and returning an error if it already exists.

---

## Read one vs many

**Rule:** “One id → return one record; no id → return a list (e.g. last 10, sorted).”

Here we **return res** only — the framework sends the model result as-is (HTTP status is typically 200). Performing read without setting **limit** returns only 1 item in the database. See more at [database operations](../database/filters-and-params.md#params-read--delete--update)

```python title="Fetching one record"
async def getItem(request: Request):
    queryParam = request.queryParam()
    if not queryParam.get("id", None):
        return response(message="id required", status=SysCodes.ATTR_MISSING, statuscode=400)

    mod = SampleModel()
    res = await mod.read(filters={"id": queryParam.get("id")}, params={"limit": 1})
    return res
```

```python title="Getting more than one item"
async def listItems(request: Request):
    mod = SampleModel()
    res = await mod.read(params={"limit": 10, "sort": -1, "sortfield": "createdAt"})
    return res
```

---

## Update only when the payload is valid

**Rule:** “Update only if we have a valid id and a valid set of fields to update.”

```python
async def updateItem(request: Request):
    body = await request.body()
    validated = await request.trimApplyRules({"id": "required,int", "name": "str", "status": "int"}, body.data if body.data else {})
    if validated.status < 0:
        return response(validated, custom=True)

    mod = SampleModel()
    res = await mod.update(filters={"id": validated.data.id}, data=validated.data, params={"limit": 1})
    return response(res, custom=True)
```

You can add rules like “only allow update if the user owns the record” by calling `request.user()` and comparing before calling the model.

---

## Delete with a required identifier

**Rule:** “Delete only when an id is provided and valid.”

```python
async def deleteItem(request: Request):
    validated = await request.trimApplyRules({"id": "required,int"}, request.queryParam())
    if validated.status < 0:
        return response(validated, custom=True)

    mod = SampleModel()
    res = await mod.delete(filters={"id": validated.data.id}, params={"limit": 1})
    return response(res, custom=True)
```

Again, you can add authorization (e.g. “only admins or the owner can delete”) before calling the model.

---

**Related:** [Database functions](../database/database-functions.md), [Filters and params](../database/filters-and-params.md), [Request functions](../requests/request-functions.md).
