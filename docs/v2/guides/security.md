# Security utilities

The framework provides helpers for hashing and secrets (e.g. under **dreema** security or helpers).

## Encrypt (or equivalent)

- **hash(value)** — Hash a value (e.g. password) for storage. Typically uses bcrypt or a similar algorithm.
- **verifyHash(plain, hashed)** — Check a plain value against a stored hash; returns True/False.
- **generateRandom([length])** — Generate a random string or bytes (e.g. for tokens).
- **getSecret([key])** — Retrieve a secret from config or env for signing/encryption.

Use **hash** when storing passwords; use **verifyHash** at login. Use **generateRandom** and **getSecret** for tokens and signed data. Exact names may be under `Encrypt` or a similar class in the codebase.
