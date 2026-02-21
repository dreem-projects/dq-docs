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

Open [http://127.0.0.1:8000/](http://127.0.0.1:8000/) in a browser, or use curl. If you see a JSON response with `"message": "Setup completed, Let's build"`, you're set up.

🎉 You're all set up! Start building your API with DQuode now.

!!! info "Changing the port"

    By default, the server runs on port **8000**. To use a different port:

    - **Via CLI:**
      Run
      ```bash
      python start.py --port=<PORT>
      ```
      replacing `<PORT>` with your desired number.

    - **Via environment variable:**
      Create a `.env` file in your project root (if one doesn't exist) and add:
      ```
      SERVER_PORT=<PORT>
      ```
      replacing `<PORT>` with your desired port number.
