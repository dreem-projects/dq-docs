!!! info "DQuode sets you up in 3 simple steps"

## 1. Get the project

Clone the DQuode project or create your app from a DQuode-based template.

## 2. Install dependencies

From the project root (e.g. `dquode/`):

```bash
pip install -r requirements.txt
```

## 3. Start the server

From the project root, run:

```bash
python start.py
```

This starts the app (Uvicorn) using defaults from **settings.json**. If the requested port is already in use, the app will try the next available port and print a message (e.g. *Port 8888 is already in use. Try next available port: 8889*).

## 4. Access your application

Open [http://127.0.0.1:8888/](http://127.0.0.1:8888/) in your browser, or use curl to access it. By default, DQuode runs on port **8888**, but you can change this to any port you prefer. If you see a JSON response with `"message": "Setup completed, Let's build"`, your setup is complete.

🎉 You're all set up! Start building your API with DQuode now.

!!! info "Configuration: settings and .env"

    Non-secret config (environment, server port, database structure) lives in **settings.json**; secrets (DB user/password, Redis password) go in **.env**. See [Config](../guides/config.md) and [Database setup](../database/setup-required.md). For [multi-database](../database/setup-required.md#multi-database), define connections in settings and pass the connection name when creating models.

!!! info "Start options"

    **Port and host**

    - Default port comes from **serverPort** in **settings.json** (default **8888**). To use another port: set **serverPort** in **settings.json** or run `python start.py --port=<PORT>`.
    - Default host is **127.0.0.1**. Override with `python start.py --host=<HOST>`.

    **Reload (development)**

    - Auto-reload is **on** when **environment** in **settings.json** is **local**, and **off** when **environment** is **live**.
    - Override: `python start.py --reload` or `python start.py --no-reload`.

    **Other CLI flags**

    | Flag | Default | Description |
    |------|---------|-------------|
    | `--port` | from **settings.json** (serverPort) or 8888 | Port to run the server on. |
    | `--host` | 127.0.0.1 | Host address. |
    | `--reload` / `--no-reload` | reload on if environment is local | Enable or disable auto-reload. |
    | `--log-level` | info | Logging level. |
    | `--workers` | 4 | Number of worker processes. |
