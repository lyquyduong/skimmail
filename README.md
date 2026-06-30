# SkimMail

**English** · [Tiếng Việt](README.vi.md) · [中文](README.zh.md)

> Skim all your inboxes. A lightweight, self-hosted, **read-first** multi-account
> email reader — IMAP core + Gmail/Outlook OAuth — packaged as a **single Go
> binary** that embeds a React SPA. No external services required by default.

**License:** AGPL-3.0-or-later (+ CLA, see [`CLA.md`](CLA.md)) · **Security:** [`SECURITY.md`](SECURITY.md)

---

## Features

- **Multi-account IMAP** read — add accounts with host/port + app-password.
- **OAuth** for Gmail & Outlook (XOAUTH2 over IMAP) — *optional, needs your own
  Google/Azure app credentials*.
- **Unified inbox** across all accounts, lightweight **threading**, **3-pane UI**
  with a sandboxed reading pane (HTML sanitized, remote images blocked).
- **Real-time** new-mail push via IMAP **IDLE → WebSocket**, with a poll-fallback
  so mail still arrives if IDLE drops — the backend syncs even when no client is open.
- **Search, 2-tier** — fast local full-text (SQLite FTS5) + optional server-side
  fallback to reach mail outside the cached window.
- **Read-fast** power tools — snooze, pin, split-inbox **bundles**, multi-select +
  bulk actions, VIP / mute senders, one-click **unsubscribe** (RFC 8058).
- **AI (opt-in, bring-your-own-key)** — summarize, classify, action-extract,
  translate via **Claude / OpenAI-compatible / Ollama**. Off by default.
- **Keyboard-first** (Superhuman-style) + **Focus/triage mode** + **PWA** (installable,
  offline shell) + **i18n** (English / Tiếng Việt / 中文, dark & light themes).
- **Pluggable storage (advanced)** — default **SQLite + filesystem**; optional
  **Postgres/MySQL** and **S3-compatible** blob store via build tags.
- **Sync scheduler** (concurrency + rate limit + priority queue) and a
  **security** layer: login rate-limit + lockout, CSRF/origin checks, SSRF guard,
  encrypted credentials at rest, and an audit log.

## Requirements

Just **Docker** — SQLite is built in, no external services needed.
*(Building from source or contributing? See [`README.dev.md`](README.dev.md).)*

---

## Quick start

```bash
docker compose up --build          # → http://localhost:8080  (SQLite + filesystem)
```

Run the **stateless stack** (Postgres + S3/MinIO) with the `advanced` profile:

```bash
docker compose --profile advanced up --build
# → skimmail:      http://localhost:8081
# → MinIO console: http://localhost:9001  (minioadmin / minioadmin)
docker compose --profile advanced down -v   # stop + wipe volumes
```

Or run the published image (distroless + non-root; data in the `/data` volume):

```bash
docker run -d -p 8080:8080 -v skimmail-data:/data \
  -e AUTH_MODE=passphrase ghcr.io/lyquyduong/skimmail:latest
```

### Install on Ubuntu / Debian (apt)

```bash
curl -fsSL https://lyquyduong.github.io/skimmail/apt/skimmail.gpg \
  | sudo tee /usr/share/keyrings/skimmail.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/skimmail.gpg] https://lyquyduong.github.io/skimmail/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/skimmail.list
sudo apt update && sudo apt install skimmail      # runs as a systemd service → http://localhost:8080
```

Config lives in `/etc/default/skimmail`; upgrades come via `apt upgrade`. All
release channels (image, `.deb`, apt repo) ship from GitHub — see
[`DISTRIBUTION.md`](DISTRIBUTION.md).

### First login

Open **http://localhost:8080**. The default auth mode is `passphrase`: **the first
password you enter becomes your master passphrase** — remember it (it's used to
log in again). Leave the username blank.

### Add a mail account

Add an **IMAP** account: email · host (e.g. `imap.gmail.com`) · port `993` ·
security `TLS` · **app password**.

> Gmail/Outlook require an **app-specific password** (not your normal password)
> unless OAuth is configured. To enable the "Add with Google/Microsoft" buttons,
> set the OAuth credentials below and a public `BASE_URL`.

Press **↻ Sync** (or wait for the background poll). New mail then pushes in
real-time over WebSocket.

---

## Usage

- **Search** — type in the search box (Enter). With a single account selected, the
  **"Search server ↗"** button also queries the mail server and caches the hits.
- **Bundles** — filter the list by Primary / Newsletter / Transactional /
  Notifications. **💤 Snoozed** shows snoozed mail.
- **Multi-select** — tick rows for **bulk** read / archive / delete.
- **Reading pane** — 🗄 Archive · 🗑 Delete (with **Undo**) · 💤 Snooze · 📌 Pin ·
  ✨ Summarize · ⚡ Actions · 🌐 Translate · ✉️ Unsubscribe · ★ VIP · 🔇 Mute.
- **AI** — open **🤖 AI settings**, enable it, pick a vendor + model + key (or use
  local **Ollama** with no key). Email content is sent to the vendor you choose.
- **Keyboard** — `?` shows all shortcuts: `j`/`k` move · `Enter`/`o` open ·
  `e` archive · `#` delete · `s` snooze · `x` select · `Space` quick-peek ·
  `/` search · **`f` Focus mode** ("Get to Zero") · `Esc` close.
- **PWA** — install from the browser address bar; the app shell works offline.
- **Language / theme** — switch EN / VI / 中 and dark/light in the sidebar.

---

## Configuration

Config is via environment variables (12-factor). See [`.env.example`](.env.example).

| Variable | Default | Purpose |
|---|---|---|
| `AUTH_MODE` | `passphrase` | `passphrase` (single-user) or `users` (multi-user) |
| `DATA_DIR` | `./data` | SQLite DB, master key, blobs |
| `LISTEN_ADDR` | `:8080` | listen address |
| `BASE_URL` | – | public URL — **required** for OAuth redirect, WebSocket, PWA |
| `TRUST_PROXY` | `false` | trust `X-Forwarded-*` behind a reverse proxy |
| `TRUSTED_ORIGINS` | – | extra CORS/CSRF-allowed origins (comma-separated) |
| `BACKGROUND_POLL_INTERVAL` | `5m` | poll-fallback period for every account |
| `MAX_CONCURRENT_SYNCS` | `4` | max mailboxes syncing at once (scheduler) |
| `SYNC_RATE_PER_MIN` | `10` | max sync starts per minute (stagger) |
| `SYNC_DEPTH_DAYS` | `30` | header sync window (0 = full history) |
| `GOOGLE_CLIENT_ID` / `_SECRET` | – | Gmail OAuth app (optional) |
| `MICROSOFT_CLIENT_ID` / `_SECRET` | – | Outlook OAuth app (optional) |
| `DB_DRIVER` | `sqlite` | `sqlite` \| `postgres` \| `mysql` (advanced) |
| `DATABASE_URL` | – | DSN when `DB_DRIVER` ≠ sqlite |
| `BLOB_STORE` | `fs` | `fs` \| `s3` (advanced) |
| `S3_ENDPOINT` / `S3_BUCKET` / `S3_REGION` / `S3_ACCESS_KEY` / `S3_SECRET_KEY` / `S3_FORCE_PATH_STYLE` | – | S3-compatible blob store |

### Advanced: Postgres / MySQL / S3

The default image is SQLite + local filesystem. To run on **Postgres/MySQL** with
an **S3-compatible** blob store, either use the Compose **`advanced` profile**
(above), or build a custom image with the backends compiled in:

```bash
docker build --build-arg TAGS="postgres s3" -t skimmail:advanced .
```

Then set `DB_DRIVER` / `DATABASE_URL` / `BLOB_STORE` / `S3_*` from the table above
(MySQL DSN needs `multiStatements=true`). Build-from-source details:
[`README.dev.md`](README.dev.md).

---

## Security

Credentials and AI/OAuth tokens are encrypted at rest (AES-256-GCM). The server
applies login rate-limiting + lockout, CSRF/origin checks, an SSRF guard on
outbound fetches, strict security headers + a sandboxed email iframe, and an
append-only audit log (`GET /api/audit`). See [`SECURITY.md`](SECURITY.md) for the
full posture and how to report a vulnerability.

## Developers

Building from source, dev mode (hot reload), VS Code setup, build tags, tests, and
project layout are in **[`README.dev.md`](README.dev.md)**.

## License

AGPL-3.0-or-later. Contributions require the [CLA](CLA.md). SPDX: `AGPL-3.0-or-later`.
