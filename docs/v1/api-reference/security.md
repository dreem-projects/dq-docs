# API reference — Security

## Auth (registers)

- **setAuthHandler(handler)** — Register the function called by **request.user()**. **handler(request)** should return a result with **data**, **status**, **message**.
- **getAuthHandler()** — Returns the currently registered auth handler (if any).
- **authenticate** — May be used internally or as a helper; see dreema implementation.

## Encrypt (or security module)

- **hash(value)** — Hash a value (e.g. password) for storage.
- **verifyHash(plain, hashed)** — Compare plain value to hash; returns bool.
- **generateRandom([length])** — Generate random string/bytes.
- **getSecret([key])** — Get secret from config/env for signing or encryption.

Exact class or module name (Encrypt, security, etc.) is defined in dreema; use these for passwords and tokens.
