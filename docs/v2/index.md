# Introduction

!!! error "What is Missing?"

    # Easy, Until Slowed by the Heart of Every API

    ##Every API developer starts fast — endpoints, responses, logic… everything seems smooth.
    Then comes the unavoidable bottleneck: the **core CRUD operations** (Create, Read, Update, Delete) **and error handling**. Wiring ORMs, configuring frameworks, and juggling dependencies slows you down before you can even deliver structured, reliable responses.

    **DQuode removes this friction.** Instantly perform CRUD, handle errors consistently, maintain predictable response structures, and focus entirely on your business logic — not on setup or boilerplate.

!!! success "What is DQuode"

- **Perform CRUD in seconds** — Your core database operations are ready to use out of the box.
- **Structured response organization** — Every response, even internal functions follows the same structure for easier maintenance and collaboration.
- **Complete backend stack** — Routing, ORM, validation, authentication, and unified response handling included.
- **Structured architecture** — Clear MVC layout with Models (data), Controllers (logic), and Views (routes).
- **Built on Python and ASGI** — Lightweight, high-performance foundation under the hood with async out of the box.

!!! failure "The Problem with Modern Backend Development"

Building a simple API with CRUD features today often turns seemingly simple tasks into a complex maze. Developers face multiple friction points:

- Excessive scaffolding and configuration before writing any logic
- Fragmented ORM setups and driver wiring (e.g., SQLAlchemy + custom integration)
- Inconsistent response formats across endpoints
- Scattered and unstandardized validation and error handling patterns
- Heavy dependency chains that slow development
- Architectural decision fatigue and harder code reviews

DQuode removes this friction, giving developers a **Fast, Structured, and Predictable backend environment** — letting you focus on what really matters: building reliable APIs with clear structure, consistent responses, and built-in CRUD and validation.

---

!!! info "The DQuode Approach"

DQuode eliminates common backend pain points by providing:

- **Lightweight built-in ORM** — [Works with supported database systems](getting-started/supported-databases.md) by switching databases via `DB_TYPE` in `.env`. No extra wiring required.
- **Clear MVC structure** — Models, Controllers, and Views have well-defined roles, making code easy to navigate and maintain.
- **Unified response contract** — Every response includes `data`, `message`, `status`, and `statuscode`, ensuring predictable frontend integration.
- **Integrated validation** — `applyRules` and `trimApplyRules` with rules like `required`, `int`, `str`, `float`, `list`, `bool`, `nullable`.
- **Centralized configuration** — `.env` via `getenv`, app-level config via `config.CONFIG` / `getconfig`, and `registers.py` for authentication and custom system codes.
- **Minimal dependencies** — Only essential libraries (see `dquode/requirements.txt`).
- **Run-and-go experience** — Clone, install, optionally configure, and start building immediately.

---

!!! note "Core Principles"
| Principle | Description |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Built-in ORM & CRUD** | Single API for `create`, `read`, `update`, `delete` works for MySQL and Mongo, letting you perform database operations instantly without extra wiring. |
| **Clear structure & predictable layout** | Models, Controllers, and Views have well-defined roles and predictable locations across projects, making maintenance and collaboration easier. |
| **Unified response & system codes** | Every response includes `data`, `message`, `status`, and `statuscode`, while `SysCodes` and `SysMessages` provide consistent backend–frontend contracts. |
| **Native-first implementation** | Stays close to Python and ASGI for high performance, clarity, and minimal abstraction. |
| **Minimal dependencies** | Only essential libraries are included, avoiding heavy or overlapping stacks. |
| **Fast setup** | Clone, install, optionally configure, and start building immediately — your app is ready in seconds. |

---

!!! question "When to Use DQuode"

DQuode is ideal for projects where you want:

- Minimal scaffolding
- [Single ORM for supported database systems](getting-started/supported-databases.md)
- Clear MVC structure
- Built-in CRUD, validation, and error handling
- Unified, predictable responses
- No framework lock-in or heavy migrations

It fits **small to large APIs, internal tools, or projects that prioritize clarity, maintainability, and structure** over maximal flexibility.
