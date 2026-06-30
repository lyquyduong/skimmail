# SkimMail

**English** · [Tiếng Việt](README.vi.md) · [中文](README.zh.md)

> Skim all your inboxes. A lightweight, self-hosted, **read-first** multi-account
> email reader — IMAP core + Gmail/Outlook OAuth — packaged as a **single Go
> binary** that embeds a React SPA. No external services required.

**License:** AGPL-3.0-or-later (+ CLA, see [`CLA.md`](CLA.md)) · **Security:** [`SECURITY.md`](SECURITY.md)

> **This is the `0.1.0` release** — a focused email reader plus account groups.
> More (AI, notifications, pluggable storage, in-app updates) is on the roadmap.

---

## Features

- **Multi-account IMAP** read — add accounts with host/port + app-password.
- **OAuth** for Gmail & Outlook (XOAUTH2 over IMAP) — *optional, needs your own
  Google/Azure app credentials*.
- **Unified inbox** across all accounts, lightweight **threading**, **3-pane UI**
  with a sandboxed reading pane (HTML sanitized, remote images blocked).
- **Account groups** — organize your accounts into named, colour-coded groups and
  read each group's mail together.
- **Real-time** new-mail push via IMAP **IDLE → WebSocket**, with a poll-fallback
  so mail still arrives if IDLE drops — the backend syncs even when no client is open.
- **Search, 2-tier** — fast local full-text (SQLite FTS5) + optional server-side
  fallback to reach mail outside the cached window.
- **Read-fast** power tools — snooze, pin, split-inbox **bundles**, multi-select +
  bulk actions, VIP / mute senders, one-click **unsubscribe** (RFC 8058).
- **Keyboard-first** (Superhuman-style) + **Focus/triage mode** + **command palette**
  (⌘K) + **PWA** (installable, offline shell) + **i18n** (English / Tiếng Việt /
  中文, dark & light themes).
- **Always-on background sync** and a **security** layer: login rate-limit +
  lockout, CSRF/origin checks, SSRF guard, encrypted credentials at rest, and an
  audit log.

## Requirements

Just **Docker** — SQLite is built in, no external services needed.
*(Building from source or contributing? See [`README.dev.md`](README.dev.md).)*

---

## Quick start

```bash
docker compose up --build          # → http://localhost:8080  (SQLite + filesystem)
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
- **Groups** — create account groups in **Settings → Groups**; pick a group to
  read just those accounts' mail.
- **Bundles** — filter the list by Primary / Newsletter / Transactional /
  Notifications. **💤 Snoozed** shows snoozed mail.
- **Multi-select** — tick rows for **bulk** read / archive / delete.
- **Reading pane** — 🗄 Archive · 🗑 Delete (with **Undo**) · 💤 Snooze · 📌 Pin ·
  ✉️ Unsubscribe · ★ VIP · 🔇 Mute.
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
| `MAX_CONCURRENT_SYNCS` | `4` | max mailboxes syncing at once |
| `SYNC_RATE_PER_MIN` | `10` | max sync starts per minute (stagger) |
| `SYNC_DEPTH_DAYS` | `30` | header sync window (0 = full history) |
| `GOOGLE_CLIENT_ID` / `_SECRET` | – | Gmail OAuth app (optional) |
| `MICROSOFT_CLIENT_ID` / `_SECRET` | – | Outlook OAuth app (optional) |

---

## Security

Credentials and OAuth tokens are encrypted at rest (AES-256-GCM). The server
applies login rate-limiting + lockout, CSRF/origin checks, an SSRF guard on
outbound fetches, strict security headers + a sandboxed email iframe, and an
append-only audit log (`GET /api/audit`). See [`SECURITY.md`](SECURITY.md) for the
full posture and how to report a vulnerability.

## Developers

Building from source, dev mode (hot reload), VS Code setup, build tags, tests, and
project layout are in **[`README.dev.md`](README.dev.md)**.

## License

AGPL-3.0-or-later. Contributions require the [CLA](CLA.md). SPDX: `AGPL-3.0-or-later`.
