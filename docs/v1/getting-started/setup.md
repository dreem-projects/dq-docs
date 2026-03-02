!!! info "DQuode sets you up in 3 simple steps"

## 1. Get the project

Clone the DQuode project or create your app from a DQuode-based template.

## 2. Install dependencies

From the project root (e.g. `dquode/`):

```bash
pip install -r requirements.txt
```

## 3. Start the server

```bash
python start.py
```

## 4. Access your application

Open [http://127.0.0.1:8888/](http://127.0.0.1:8888/) in your browser, or use curl to access it. By default, DQuode runs on port **8888**, but you can change this to any port you prefer. If you see a JSON response with `"message": "Setup completed, Let's build"`, your setup is complete.

🎉 You're all set up! Start building your API with DQuode now.

!!! info "Configuration: settings and .env"

    Non-secret config (environment, server port, database structure) lives in **settings.json**; secrets (DB user/password, Redis password) go in **.env**. See [Config](../guides/config.md) and [Database setup](../database/setup-required.md). For [multi-database](../database/setup-required.md#multi-database), define connections in settings and pass the connection name when creating models.

!!! info "Changing the port"

    By default, the server runs on port **8888**. To use a different port:

    - **Via settings:** Set **serverPort** in **settings.json** (e.g. `"serverPort": 8000`).
    - **Via CLI:** Run `python start.py --port=<PORT>`.
    - **Via .env:** Add `SERVER_PORT=<PORT>` to your **.env** file.
