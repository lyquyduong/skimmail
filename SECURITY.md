# Security Policy

SkimMail is a self-hosted, read-first email reader. The security posture below
is implemented, not aspirational; the [Security page on the
wiki](https://github.com/lyquyduong/skimmail/wiki/Security) covers how to
operate it.

## Reporting a vulnerability

Please report security issues **privately** — do not open a public issue.
Email <skimmail@base101.app> (or use GitHub private vulnerability reporting)
with:

- a description and impact,
- steps to reproduce / PoC,
- affected version / commit.

We aim to acknowledge within a few days and to coordinate a fix + disclosure.
Please give us reasonable time to release a fix before public disclosure.

## Security posture (implemented)

- **Transport / headers** — CSP (`default-src 'self'`, no remote script), HSTS,
  `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `X-Frame-Options: DENY`,
  `Cross-Origin-Opener-Policy`, `Cross-Origin-Resource-Policy`,
  `Permissions-Policy`. Email bodies render in a **sandboxed iframe** and are
  HTML-sanitized (bluemonday) with **remote content blocked** by default.
- **Auth / sessions** — Argon2id password hashing; opaque DB-backed session in an
  HttpOnly/Secure/SameSite=Lax cookie; uniform login errors (no account
  enumeration); **per-IP login rate-limit + lockout** after repeated failures;
  global per-IP request rate-limit; **CSRF origin check** on state-changing
  requests.
- **SSRF guard** — outbound fetches of attacker-influenceable URLs (one-click
  unsubscribe) use an SSRF-hardened client that refuses loopback / private /
  link-local / cloud-metadata addresses and pins the validated IP (DNS-rebind
  safe).
- **Secrets at rest** — account credentials, AI/OAuth tokens, TOTP secrets, proxy
  URLs, and the S3-storage secret key are encrypted with AES-256-GCM under a 32-byte
  master key; secrets are not logged; the audit log redacts to non-secret detail. The
  storage API returns `secret_key_set` only, never the S3 secret.
- **Master-key protection (`KEY_PROVIDER`)** — how that master key is stored on disk:
  `file` (default) keeps it raw at `DATA_DIR/master.key` (protected by file
  permissions; pair with an encrypted volume), or `passphrase` stores only a
  **wrapped** key in `DATA_DIR/keyring.json`, encrypted with a KEK derived (Argon2id)
  from a **separate install-level unlock secret** (`SKIMMAIL_UNLOCK_PASSPHRASE[_FILE]`,
  or an interactive prompt). With `passphrase`, copying `DATA_DIR` without the secret
  reveals nothing. The secret is decoupled from the login passphrase (background sync
  keeps working) and rotatable without re-encrypting data via `skimmail rekey`.
- **Mail content at rest** — email headers, bodies (`DATA_DIR/blobs/`), and the DB
  file itself are **plaintext**; encrypt them at the volume level (LUKS/dm-crypt,
  encrypted ZFS/EBS, S3 SSE). See [docs/ENCRYPTION-AT-REST.md](docs/ENCRYPTION-AT-REST.md).
- **Storage engine config** — the FS⇄S3 switch (§23) is admin-gated and
  audit-logged. The S3 endpoint is operator-supplied (admin trust boundary),
  used only for the configured blob bucket; blobs are streamed through the app
  (no public bucket / no presigned URLs) so sanitize + auth stay centralized.
- **Self-update integrity** — in-app updates are downloaded only over HTTPS and
  **verified before they're applied**: a minisign (ed25519) signature over
  `SHA256SUMS` is checked against an embedded public key, then the downloaded
  asset's SHA-256 is matched. An unsigned/mismatched download is never
  installed. Update checking is opt-in (the only phone-home), admin-gated, and
  audit-logged; the binary is swapped atomically with a `.old` backup.
- **Audit log** — append-only record of login, account add, OAuth grant, and
  settings changes (`GET /api/audit`).
- **Supply chain / runtime** — `govulncheck` (+ osv-scanner / pnpm audit) in CI;
  pinned deps; distroless non-root container; binary-size budget.

## Known gaps / roadmap (PLAN §24)

These are tracked and not yet implemented:

- 2FA (TOTP) / WebAuthn passkeys + recovery codes; remote session revocation list.
- Master-key wrap **shipped** (`KEY_PROVIDER=passphrase`, §24). Still open: an
  `age`/Vault/cloud-KMS provider and an OS-keystore provider (Keychain/DPAPI/libsecret).
- Nonce-based CSP (drop `style-src 'unsafe-inline'`) — needs FE inline-style refactor.
- MIME-parser depth/size limits + decompression-bomb guard + parser fuzzing.
- Content (bodies/headers/DB) at rest is via **volume encryption** today
  (docs/ENCRYPTION-AT-REST.md). Optional in-app whole-DB encryption (SQLCipher) would
  require cgo; encrypted backups; full master-key (DEK) rotation with re-encrypt.
- Signed releases (cosign / SLSA provenance) + SBOM publishing.
- Multi-backend (SQLite/Postgres/MySQL) security test matrix in CI.
