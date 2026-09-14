<!-- PUBLISHING NOTE — scripts/sync-readme-to-github.sh pushes THIS file to
     github.com/lyquyduong/skimmail as README.md, and it is also published
     there as README.vi.md and README.zh.md (this file already carries all
     three languages). As of 2026-09-13 that branch also carries LICENSE,
     SECURITY.md and CLA.md, so the relative links below resolve.
     COMMERCIAL-TERMS.md and docs/LICENSING.md are deliberately NOT published
     — COMMERCIAL-TERMS.md marks itself a draft pending legal review — so
     nothing here may link to them; the licensing paragraphs point at LICENSE
     and at skimmail@base101.app instead. Links that will never be published
     (.env.example, README.dev.md) already point at the wiki.
     Contact address is skimmail@base101.app and nothing else: never put a
     personal or employer address on this page. -->

<div align="center">

# 📬 SkimMail

### Skim all your inboxes. One app. Zero cloud.

**The lightweight, self-hosted, read-first email reader for people with too many inboxes.**

[![License: BSL-1.1](https://img.shields.io/badge/License-BSL--1.1-blue.svg)](LICENSE)
[![Release](https://img.shields.io/github/v/release/lyquyduong/skimmail?label=Release)](https://github.com/lyquyduong/skimmail/releases)
[![Docker](https://img.shields.io/badge/ghcr.io-skimmail-2496ED?logo=docker&logoColor=white)](https://ghcr.io/lyquyduong/skimmail)
[![Go](https://img.shields.io/badge/Go-single%20binary-00ADD8?logo=go&logoColor=white)](#)
[![PWA](https://img.shields.io/badge/PWA-installable-5A0FC8?logo=pwa)](#)
[![i18n](https://img.shields.io/badge/i18n-EN%20·%20VI%20·%20中文-success)](#)

**[English](#english)** · **[Tiếng Việt](#tiếng-việt)** · **[中文](#中文)**

</div>

<p align="center">
  <img src="https://raw.githubusercontent.com/lyquyduong/skimmail/main/docs/screenshot-hero.png" alt="SkimMail: five accounts in three groups down the left, a unified inbox in the middle with per-bundle counts and colour-coded tags, and an opened message with its attachment on the right." width="100%">
</p>

<!-- HERO SCREENSHOT — v1.13.0, replacing the v0.1.0 capture that showed one
     account and an empty third pane under a headline about people with too many
     inboxes.
     The src is an ABSOLUTE raw.githubusercontent URL on purpose. A relative
     docs/ path 404s here: the published branch carries only the READMEs plus
     LICENSE, SECURITY.md and CLA.md. That means the PNG must be pushed to the
     GitHub repo at docs/screenshot-hero.png for this to render at all — if you
     sync this file without pushing the image, the hero is a broken-image icon
     on the one page this file exists to serve. That is exactly how the previous
     hero broke.
     What it shows: a THROWAWAY instance — a temporary DATA_DIR seeded with 5
     accounts and 20 invented messages at reserved example domains (RFC 2606).
     No real mail was captured. -->

```text
   work@corp       ──▶  WireGuard tunnel   ──▶   203.0.113.10
   me@personal     ──▶  SOCKS5 proxy       ──▶   198.51.100.42
   side@project    ──▶  serverless relay   ──▶   a new address each time
   alerts@ops      ──▶  Direct             ──▶   your own address
```

<div align="center">

**One inbox. One binary. A different way out for every account.**

<sub>Addresses shown are examples (RFC 5737 documentation ranges), not real hosts.</sub>

</div>

---

<a id="english"></a>

## 🇬🇧 English

### ✨ Why SkimMail?

You have five inboxes and zero time. **SkimMail** puts *all* of them — Gmail, Outlook, and any IMAP account — into one blazing-fast, keyboard-first reading experience that **you host yourself**.

No cloud middleman reading your mail. No per-seat subscription. No 2 GB Electron app. Just a **single Go binary** with a beautiful embedded React interface, backed by SQLite — install it in under a minute with Docker or `apt`, and own your email workflow again.

> 🎯 **Read-first by design.** SkimMail is built for *triaging and reading* at speed — snooze it, pin it, bundle it, archive it, get to zero.

|  | SkimMail | Typical webmail | Desktop clients |
|---|---|---|---|
| Self-hosted, your data stays home | ✅ | ❌ | ⚠️ local only |
| All accounts in one unified inbox | ✅ | ❌ | ⚠️ |
| Single 21 MB binary, no external services | ✅ | — | ❌ |
| Keyboard-first triage (Superhuman-style) | ✅ | ❌ | ⚠️ |
| **Per-account exit IP** — proxy / WireGuard / relay, set in the app | ✅ | ❌ | ⚠️ external tooling |
| Free Community Edition + paid Pro tier | ✅ | varies | varies |

### 🚀 Features

**📥 Read everything, everywhere**
- **Multi-account IMAP** — add any provider with host/port + app-password.
- **Gmail & Outlook OAuth** (XOAUTH2 over IMAP) — optional, bring your own Google/Azure app credentials.
- **Unified inbox** across all accounts with lightweight **conversation threading**.
- **3-pane UI** with a fully **sandboxed reading pane** — HTML sanitized, remote images blocked by default.
- **Account groups** — organize accounts into named, colour-coded groups and read each group together.

**⚡ Real-time & search**
- **Instant new-mail push**: IMAP **IDLE → WebSocket**, with an automatic poll-fallback so mail keeps flowing even if IDLE drops.
- **Always-on background sync** — the server syncs even when no browser is open.
- **2-tier search** — millisecond local full-text search (SQLite FTS5) plus optional server-side fallback to reach mail beyond the cached window.

**🧰 Read-fast power tools**
- **Snooze** · **Pin** · split-inbox **Bundles** (Primary / Newsletter / Transactional / Notifications).
- **Multi-select + bulk actions** (read / archive / delete with **Undo**).
- **VIP** and **Mute** senders · one-click **Unsubscribe** (RFC 8058).
- **Keyboard-first** (Superhuman-style shortcuts, press `?`) · **Focus/triage mode** ("Get to Zero") · **Command palette** (⌘K).

**🕸️ A different way out for every account** — *the part other mail clients leave to you*
- **Per-account egress.** Send each account's mail through its own **SOCKS5 proxy**, **HTTP proxy**, **embedded WireGuard tunnel**, or a **serverless relay** you deploy yourself — paste one file into Cloudflare Workers, or run `skimmail relay` in a container. Work and personal never have to share an exit IP.
- **No external plumbing.** Mail clients normally leave this to `wireproxy`, a per-app VPN or a second container. Here it is a dropdown on the account.
- **Proof, not just configuration.** Every connection reports the **public IP it actually leaves from** — proxy, tunnel, relay and Direct alike — and SkimMail **warns you when two accounts meant to be separate arrive from the same address**. **Test** opens a real connection through that egress and reports latency.
- **Fail-closed.** Switch a connection off and its accounts go **degraded** and stop syncing — they are never quietly dropped back onto your home IP.

**🤖 Rules, alerts & operations**
- **Rules & Signals** — match on sender, subject (text or regex), account, group, or "looks like a one-time code", then notify a channel, toast in-app, send a push, or apply a clickable **tag**. One-time codes get an **OTP** chip that copies the code; the code is never stored.
- **Six notification channels** — plain **webhook**, **Telegram**, **Slack**, **Discord**, **Microsoft Teams**, **Google Chat** — each with its own **Send test**, plus **Web Push** to browser and phone (standard VAPID keys SkimMail generates itself; no Firebase account), with quiet hours and a per-account toggle.
- **Sync health** — an account that keeps failing **stops on its own** instead of hammering your provider; a board shows Stopped / Degraded / Paused with one-click Resume, Retry, Stop, and alerts fire when a sync stops, a proxy or tunnel goes down, or an OAuth sign-in is about to expire.
- **Backup & restore** — an encrypted snapshot of the whole instance that restores onto any SkimMail server, even one on a different database, plus scheduled backups to a local folder or any S3-compatible storage (MinIO, Cloudflare R2, Backblaze B2, Wasabi, AWS S3).
- **Remote access without port-forwarding** — publish SkimMail through a **Cloudflare Tunnel** or **Tailscale**, with a QR code for your phone. Those helpers and the WireGuard engine are **plugins downloaded on demand** against a signed, SHA256-pinned index; install, update and remove them from **Settings ▸ Plugins**.
- **Configured in the browser** — public URL, OAuth apps and sign-in mode are settings in the app; anything set in the environment still wins and shows as locked, naming the variable. **In-app self-update** installs a new release in one click on binary/apt installs.

**🌍 Platform**
- **PWA** — install from the browser; the app shell works offline.
- **i18n** — English · Tiếng Việt · 中文, with dark & light themes.
- **One package**: a single `skimmail` binary — **21 MB on disk, an 8.7 MB download** (measured on the published v1.11.1 `linux/amd64` build). WireGuard egress, PostgreSQL, MySQL and S3 are all compiled in; the WireGuard engine arrives as a SHA256-pinned plugin the first time you add a tunnel.

**🔐 Security & multi-user**
- Credentials, OAuth tokens **and cached message bodies** are **encrypted at rest (AES-256-GCM)**.
- **Remote images stay blocked until you ask** — and then **SkimMail fetches them, not your browser**, through that account's own egress, so the sender's server never sees you.
- A one-time **claim code** gates the very first sign-in, so whoever reaches the port first cannot claim your instance.
- **Real multi-user** — `AUTH_MODE=users` gives each person their own accounts, mail and settings, invisible to everyone else, as **owner**, **operator** or **viewer**.
- Login **rate-limiting + lockout**, **2FA**, a server-enforced idle lock, **CSRF/origin checks**, an **SSRF guard**, **signed releases and a signed plugin index**, and an **append-only audit log**.
- Full posture & responsible disclosure: [`SECURITY.md`](SECURITY.md).

> 🧭 **Everything above is shipped.** **AI Triage is the only headline feature still on the roadmap** — see the [changelog](https://github.com/lyquyduong/skimmail/releases) for what landed when.

### 📦 Installation

The only requirement is **Docker** (or Ubuntu/Debian for `apt`). SQLite is built in — **no external services**.

#### 🐳 Docker

```bash
docker run -d --name skimmail -p 8080:8080 -v skimmail-data:/data \
  -e AUTH_MODE=passphrase ghcr.io/lyquyduong/skimmail:latest
```

#### 🧩 Docker Compose

```bash
curl -fsSLO https://lyquyduong.github.io/skimmail/docker-compose.yml
docker compose up -d      # → http://localhost:8080
```

#### 📦 APT (Ubuntu / Debian)

```bash
curl -fsSL https://lyquyduong.github.io/skimmail/apt/skimmail.gpg \
  | sudo tee /usr/share/keyrings/skimmail.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/skimmail.gpg] https://lyquyduong.github.io/skimmail/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/skimmail.list
sudo apt update && sudo apt install skimmail
sudo systemctl status skimmail                 # → http://localhost:8080
```

Runs as a systemd service. Config: `/etc/default/skimmail` · Data: `/var/lib/skimmail` · Logs: `/var/log/skimmail/skimmail.log` + `journalctl -u skimmail` (level/rotation/realtime viewer in **Settings ▸ Logs**) · Upgrades: `sudo apt upgrade`. Embedded WireGuard egress is in this one package — the engine downloads as a SHA256-pinned plugin when you enable it.

#### 🔑 First login

Open **http://localhost:8080**. In the default `passphrase` mode, **the first password you enter becomes your master passphrase** — remember it. Leave the username blank. Then add an IMAP account (email · host · port `993` · TLS · **app password**) and press **↻ Sync**.

> Full configuration reference (OAuth, reverse proxy, sync tuning, every environment variable): **[wiki ▸ Configuration](https://github.com/lyquyduong/skimmail/wiki/Configuration)**.

#### 🗑️ Uninstall (complete removal)

Uninstalling keeps your data (accounts, mail cache, the encryption master key) unless you also delete it — the ⚠ steps are permanent. Same commands for both editions.

```bash
# Docker Compose
docker compose down -v && rm docker-compose.yml                  # ⚠ containers + data volumes

# Docker (docker run)
docker rm -f skimmail && docker volume rm skimmail-data          # ⚠ all data

# APT (Ubuntu / Debian)
sudo apt purge skimmail
sudo rm -rf /var/lib/skimmail /var/log/skimmail && sudo userdel skimmail   # ⚠ data + logs + service user
sudo rm -f /etc/apt/sources.list.d/skimmail.list /usr/share/keyrings/skimmail.gpg && sudo apt update
```

> Step-by-step details (incl. removing the images / manual-binary installs): the **Uninstall** sections in **[wiki ▸ Installation](https://github.com/lyquyduong/skimmail/wiki/Installation)**.

### 📄 License

SkimMail is proprietary software, licensed under **[Business Source License 1.1](LICENSE)** (proprietary until July 23, 2030; converts to AGPL-3.0+ after).

- **Community Edition** — Free, no license key required; 10 accounts / 1 sign-in / 3 proxies / 2 groups / 5 GB storage
- **Sponsor Edition** — Thank-you for backing SkimMail (GitHub Sponsors, Open Collective, Ko-fi, direct donation); 25 accounts / 3 sign-ins / 5 proxies / 5 groups / 15 GB
- **Pro Edition** — Paid license key; unlimited features + future AI Triage service

The licence itself is [`LICENSE`](LICENSE); commercial terms for Sponsor and Pro keys are available on request at <skimmail@base101.app>.
- Contributions require a **[Contributor License Agreement](CLA.md)** that grants the maintainer rights to use your work under the current proprietary license and any future license (including AGPL after 2030).

### 🤝 Contributing

We'd love your help making SkimMail better!

1. **🐛 Bugs & ideas** — open an issue at **[github.com/lyquyduong/skimmail/issues](https://github.com/lyquyduong/skimmail/issues)**. Include your version (`skimmail --version` or image tag), platform, and steps to reproduce.
2. **🔧 Code contributions** — the primary development repository is currently private; **email the maintainer first** at **skimmail@base101.app** to discuss your change before writing code. All contributions require signing the [CLA](CLA.md).
3. **🔒 Security issues** — please **do not** open a public issue; follow [`SECURITY.md`](SECURITY.md).
4. **🌐 Translations & docs** — improvements to EN/VI/ZH strings and documentation are very welcome.

Developer setup (build from source, hot reload, tests) is shared with contributors once the CLA is signed — start with step 2 above.

### ❤️ Support the project

If SkimMail saves you time every day, consider fueling its development:

| Channel | Link |
|---|---|
| 💖 GitHub Sponsors | <!-- TODO: add link --> *coming soon* |
| ☕ Buy Me a Coffee | <!-- TODO: add link --> *coming soon* |
| ₿ Crypto (BTC / ETH / USDT) | <!-- TODO: add wallet addresses --> *coming soon* |

⭐ **Starring the [GitHub repo](https://github.com/lyquyduong/skimmail) is free and helps a lot!**

---

<a id="tiếng-việt"></a>

## 🇻🇳 Tiếng Việt

### ✨ Vì sao chọn SkimMail?

Bạn có năm hộp thư và không có thời gian. **SkimMail** gom *tất cả* — Gmail, Outlook và mọi tài khoản IMAP — vào một trải nghiệm đọc siêu nhanh, điều khiển bằng bàn phím, và **tự lưu trữ trên máy chủ của chính bạn**.

Không có "đám mây trung gian" nào đọc thư của bạn. Không phí thuê bao theo người dùng. Không app Electron nặng 2 GB. Chỉ một **file nhị phân Go duy nhất** nhúng sẵn giao diện React tuyệt đẹp, chạy trên SQLite — cài đặt dưới một phút bằng Docker hoặc `apt`, và làm chủ email của mình trở lại.

> 🎯 **Thiết kế "đọc-trước" (read-first).** SkimMail sinh ra để *phân loại và đọc* thật nhanh — hoãn (snooze), ghim, gom nhóm, lưu trữ — đưa hộp thư về con số 0.

|  | SkimMail | Webmail thông thường | Ứng dụng desktop |
|---|---|---|---|
| Tự lưu trữ, dữ liệu ở nhà bạn | ✅ | ❌ | ⚠️ chỉ cục bộ |
| Mọi tài khoản trong một hộp thư hợp nhất | ✅ | ❌ | ⚠️ |
| Một binary 21 MB, không cần dịch vụ ngoài | ✅ | — | ❌ |
| Thao tác bàn phím tốc độ cao (kiểu Superhuman) | ✅ | ❌ | ⚠️ |
| **IP ra riêng cho từng tài khoản** — proxy / WireGuard / relay, chỉnh ngay trong app | ✅ | ❌ | ⚠️ phải dùng công cụ ngoài |
| Community Edition miễn phí + bậc Pro trả phí | ✅ | tuỳ | tuỳ |

### 🚀 Tính năng

**📥 Đọc mọi thứ, ở mọi nơi**
- **IMAP đa tài khoản** — thêm bất kỳ nhà cung cấp nào với host/port + mật khẩu ứng dụng.
- **OAuth cho Gmail & Outlook** (XOAUTH2 qua IMAP) — tùy chọn, dùng thông tin ứng dụng Google/Azure của riêng bạn.
- **Hộp thư hợp nhất** cho mọi tài khoản, kèm **gom hội thoại (threading)** gọn nhẹ.
- **Giao diện 3 khung** với **khung đọc được sandbox hoàn toàn** — HTML được làm sạch, ảnh từ xa bị chặn mặc định.
- **Nhóm tài khoản** — tổ chức tài khoản thành các nhóm có tên, có màu riêng và đọc thư của cả nhóm cùng lúc.

**⚡ Thời gian thực & tìm kiếm**
- **Đẩy thư mới tức thì**: IMAP **IDLE → WebSocket**, kèm cơ chế poll dự phòng tự động nên thư vẫn về đều nếu IDLE rớt kết nối.
- **Đồng bộ nền liên tục** — server vẫn đồng bộ ngay cả khi không có trình duyệt nào mở.
- **Tìm kiếm 2 tầng** — tìm toàn văn cục bộ trong mili-giây (SQLite FTS5) + tùy chọn tìm tiếp trên máy chủ mail để với tới thư ngoài vùng cache.

**🧰 Công cụ đọc-nhanh**
- **Snooze (hoãn)** · **Ghim** · **Bundles** chia hộp thư (Chính / Bản tin / Giao dịch / Thông báo).
- **Chọn nhiều + thao tác hàng loạt** (đọc / lưu trữ / xóa, có **Hoàn tác**).
- Người gửi **VIP** và **Tắt tiếng (Mute)** · **Hủy đăng ký** một chạm (RFC 8058).
- **Ưu tiên bàn phím** (phím tắt kiểu Superhuman, nhấn `?`) · **Chế độ Focus/phân loại** ("Get to Zero") · **Bảng lệnh** (⌘K).

**🕸️ Mỗi tài khoản một đường ra riêng** — *phần mà các trình đọc mail khác để bạn tự lo*
- **Egress theo từng tài khoản.** Cho mail của mỗi tài khoản đi qua **proxy SOCKS5**, **proxy HTTP**, **tunnel WireGuard nhúng**, hoặc một **relay serverless** bạn tự triển khai — dán một file vào Cloudflare Workers, hoặc chạy `skimmail relay` trong container. Công việc và cá nhân không buộc phải chung một IP ra.
- **Không cần lắp ráp bên ngoài.** Các trình đọc mail khác thường đẩy việc này cho `wireproxy`, VPN theo ứng dụng hay một container thứ hai. Ở đây nó chỉ là một ô chọn trong tài khoản.
- **Bằng chứng, không chỉ là cấu hình.** Mỗi kết nối báo về **địa chỉ IP công khai mà nó thực sự đi ra** — proxy, tunnel, relay và cả Direct — và SkimMail **cảnh báo khi hai tài khoản đáng lẽ tách biệt lại đi ra từ cùng một địa chỉ**. Nút **Test** mở một kết nối thật qua egress đó và báo độ trễ.
- **Fail-closed.** Tắt một kết nối thì các tài khoản của nó chuyển sang **degraded** và ngừng đồng bộ — không bao giờ bị âm thầm đẩy về IP nhà bạn.

**🤖 Rules, cảnh báo & vận hành**
- **Rules & Signals** — khớp theo người gửi, tiêu đề (chữ thường hoặc regex), tài khoản, nhóm, hoặc "trông giống mã một lần", rồi báo về một kênh, hiện toast trong app, gửi push, hoặc gắn **tag** bấm được để lọc. Mail chứa mã một lần có chip **OTP** để copy; bản thân mã không bao giờ được lưu.
- **Sáu kênh thông báo** — **webhook** thường, **Telegram**, **Slack**, **Discord**, **Microsoft Teams**, **Google Chat** — mỗi kênh có **Send test** riêng, cộng thêm **Web Push** về trình duyệt và điện thoại (khoá VAPID chuẩn do SkimMail tự sinh; không cần tài khoản Firebase), kèm giờ yên lặng và công tắc theo từng tài khoản.
- **Sức khoẻ đồng bộ** — tài khoản liên tục lỗi sẽ **tự dừng** thay vì dội vào nhà cung cấp; bảng theo dõi hiện Stopped / Degraded / Paused với Resume, Retry, Stop một chạm, và cảnh báo bắn ra khi một tài khoản ngừng đồng bộ, một proxy/tunnel chết, hoặc một đăng nhập OAuth sắp hết hạn.
- **Sao lưu & khôi phục** — bản chụp mã hoá của toàn bộ instance, khôi phục được lên bất kỳ máy chủ SkimMail nào kể cả máy dùng database khác, cộng thêm sao lưu theo lịch về thư mục cục bộ hoặc bất kỳ lưu trữ tương thích S3 nào (MinIO, Cloudflare R2, Backblaze B2, Wasabi, AWS S3).
- **Remote access không cần mở cổng** — đưa SkimMail ra ngoài qua **Cloudflare Tunnel** hoặc **Tailscale**, kèm mã QR cho điện thoại. Các chương trình đó và engine WireGuard là **plugin tải theo nhu cầu**, đối chiếu một chỉ mục đã ký và ghim SHA256; cài, cập nhật, gỡ ngay tại **Settings ▸ Plugins**.
- **Cấu hình ngay trong trình duyệt** — URL công khai, app OAuth và chế độ đăng nhập là thiết lập trong app; thứ gì đặt bằng biến môi trường vẫn thắng và hiện ra dưới dạng khoá, kèm tên biến. **Tự cập nhật trong app** cài bản mới bằng một cú bấm trên các bản cài binary/apt.

**🌍 Nền tảng**
- **PWA** — cài từ trình duyệt; khung ứng dụng hoạt động offline.
- **Đa ngôn ngữ** — English · Tiếng Việt · 中文, giao diện sáng & tối.
- **Một gói duy nhất**: một binary `skimmail` — **21 MB trên đĩa, tải về 8,7 MB** (đo trên bản v1.11.1 `linux/amd64` đã phát hành). WireGuard egress, PostgreSQL, MySQL và S3 đều được biên dịch sẵn; engine WireGuard về dưới dạng plugin ghim SHA256 ngay lần đầu bạn thêm tunnel.

**🔐 Bảo mật & đa người dùng**
- Thông tin đăng nhập, token OAuth **và nội dung thư đã cache** đều **mã hoá khi lưu trữ (AES-256-GCM)**.
- **Ảnh từ xa vẫn bị chặn cho tới khi bạn yêu cầu** — và khi đó **SkimMail tải ảnh, không phải trình duyệt của bạn**, đi qua đúng egress của tài khoản đó, nên máy chủ người gửi không thấy bạn.
- Một **mã claim dùng một lần** chặn ngay lần đăng nhập đầu tiên, để người chạm tới cổng trước không chiếm được instance của bạn.
- **Đa người dùng thật sự** — `AUTH_MODE=users` cho mỗi người tài khoản, mail và thiết lập của riêng mình, người khác không thấy được, với vai trò **owner**, **operator** hoặc **viewer**.
- **Giới hạn tần suất đăng nhập + khóa tài khoản**, **2FA**, khoá màn hình khi rảnh do server cưỡng chế, **kiểm tra CSRF/origin**, **chống SSRF**, **bản phát hành và chỉ mục plugin đều được ký**, và **nhật ký audit chỉ-ghi-thêm**.
- Chi tiết & cách báo cáo lỗ hổng: [`SECURITY.md`](SECURITY.md).

> 🧭 **Mọi thứ ở trên đều đã phát hành.** **AI Triage là tính năng lớn duy nhất còn nằm trong roadmap** — xem [changelog](https://github.com/lyquyduong/skimmail/releases) để biết cái gì về lúc nào.

### 📦 Cài đặt

Yêu cầu duy nhất là **Docker** (hoặc Ubuntu/Debian nếu dùng `apt`). SQLite tích hợp sẵn — **không cần dịch vụ ngoài**.

#### 🐳 Docker

```bash
docker run -d --name skimmail -p 8080:8080 -v skimmail-data:/data \
  -e AUTH_MODE=passphrase ghcr.io/lyquyduong/skimmail:latest
```

#### 🧩 Docker Compose

```bash
curl -fsSLO https://lyquyduong.github.io/skimmail/docker-compose.yml
docker compose up -d      # → http://localhost:8080
```

#### 📦 APT (Ubuntu / Debian)

```bash
curl -fsSL https://lyquyduong.github.io/skimmail/apt/skimmail.gpg \
  | sudo tee /usr/share/keyrings/skimmail.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/skimmail.gpg] https://lyquyduong.github.io/skimmail/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/skimmail.list
sudo apt update && sudo apt install skimmail
sudo systemctl status skimmail                 # → http://localhost:8080
```

Chạy dưới dạng dịch vụ systemd. Cấu hình: `/etc/default/skimmail` · Dữ liệu: `/var/lib/skimmail` · Log: `/var/log/skimmail/skimmail.log` + `journalctl -u skimmail` (level/xoay vòng/xem realtime ở **Settings ▸ Logs**) · Nâng cấp: `sudo apt upgrade`. WireGuard egress nhúng nằm ngay trong gói này — engine tải về dưới dạng plugin ghim SHA256 khi bạn bật.

#### 🔑 Đăng nhập lần đầu

Mở **http://localhost:8080**. Ở chế độ mặc định `passphrase`, **mật khẩu đầu tiên bạn nhập sẽ trở thành passphrase chính** — hãy nhớ kỹ. Để trống tên đăng nhập. Sau đó thêm tài khoản IMAP (email · host · port `993` · TLS · **mật khẩu ứng dụng**) và nhấn **↻ Sync**.

> Tài liệu cấu hình đầy đủ (OAuth, reverse proxy, tinh chỉnh đồng bộ, mọi biến môi trường): **[wiki ▸ Cấu hình](https://github.com/lyquyduong/skimmail/wiki/Configuration-vi)**.

#### 🗑️ Gỡ cài đặt (xoá hoàn toàn)

Gỡ cài đặt không tự xoá dữ liệu (tài khoản, cache mail, master key mã hoá) trừ khi bạn xoá luôn — các bước ⚠ là vĩnh viễn. Lệnh dùng chung cho cả hai bản.

```bash
# Docker Compose
docker compose down -v && rm docker-compose.yml                  # ⚠ container + volume dữ liệu

# Docker (docker run)
docker rm -f skimmail && docker volume rm skimmail-data          # ⚠ toàn bộ dữ liệu

# APT (Ubuntu / Debian)
sudo apt purge skimmail
sudo rm -rf /var/lib/skimmail /var/log/skimmail && sudo userdel skimmail   # ⚠ dữ liệu + log + user service
sudo rm -f /etc/apt/sources.list.d/skimmail.list /usr/share/keyrings/skimmail.gpg && sudo apt update
```

> Hướng dẫn chi tiết từng bước (kể cả xoá image / bản cài binary thủ công): mục **Gỡ cài đặt** trong **[wiki ▸ Cài đặt](https://github.com/lyquyduong/skimmail/wiki/Installation-vi)**.

### 📄 Giấy phép

SkimMail là phần mềm độc quyền, cấp phép theo **[Business Source License 1.1](LICENSE)** (độc quyền đến 23/07/2030; sau đó chuyển sang AGPL-3.0+).

- **Community Edition** — Miễn phí, không cần license key; 10 tài khoản / 1 tài khoản đăng nhập / 3 proxy / 2 nhóm / 5 GB lưu trữ
- **Sponsor Edition** — Lời cảm ơn dành cho người ủng hộ SkimMail (GitHub Sponsors, Open Collective, Ko-fi, quyên góp trực tiếp); 25 tài khoản / 3 tài khoản đăng nhập / 5 proxy / 5 nhóm / 15 GB
- **Pro Edition** — License key trả phí; không giới hạn + dịch vụ AI Triage trong tương lai

Giấy phép nằm ở [`LICENSE`](LICENSE); điều khoản thương mại cho key Sponsor và Pro xin liên hệ <skimmail@base101.app>.
- Mọi đóng góp cần chấp thuận **[Thỏa thuận cấp phép cho người đóng góp (CLA)](CLA.md)**, cho phép maintainer sử dụng đóng góp của bạn theo giấy phép độc quyền hiện tại và mọi giấy phép trong tương lai (bao gồm AGPL sau 2030).

### 🤝 Đóng góp

Chúng tôi rất mong nhận được sự giúp đỡ của bạn!

1. **🐛 Báo lỗi & đề xuất** — tạo issue tại **[github.com/lyquyduong/skimmail/issues](https://github.com/lyquyduong/skimmail/issues)**. Nhớ ghi phiên bản (`skimmail --version` hoặc tag image), nền tảng và các bước tái hiện lỗi.
2. **🔧 Đóng góp mã nguồn** — repository phát triển chính hiện ở chế độ riêng tư; vui lòng **email cho maintainer trước** tại **skimmail@base101.app** để trao đổi trước khi viết code. Mọi đóng góp cần ký [CLA](CLA.md).
3. **🔒 Lỗ hổng bảo mật** — vui lòng **không** tạo issue công khai; làm theo hướng dẫn trong [`SECURITY.md`](SECURITY.md).
4. **🌐 Dịch thuật & tài liệu** — mọi cải thiện cho chuỗi ngôn ngữ EN/VI/ZH và tài liệu đều rất được hoan nghênh.

Hướng dẫn dành cho lập trình viên (build từ source, hot reload, test) được chia sẻ cho người đóng góp sau khi ký CLA — bắt đầu từ bước 2 ở trên.

### ❤️ Ủng hộ dự án

Nếu SkimMail giúp bạn tiết kiệm thời gian mỗi ngày, hãy cân nhắc tiếp sức cho dự án:

| Kênh | Liên kết |
|---|---|
| 💖 GitHub Sponsors | <!-- TODO: thêm link --> *sắp có* |
| ☕ Buy Me a Coffee | <!-- TODO: thêm link --> *sắp có* |
| ₿ Crypto (BTC / ETH / USDT) | <!-- TODO: thêm địa chỉ ví --> *sắp có* |

⭐ **Thả sao cho [repo GitHub](https://github.com/lyquyduong/skimmail) — miễn phí mà ý nghĩa lắm!**

---

<a id="中文"></a>

## 🇨🇳 中文

### ✨ 为什么选择 SkimMail？

你有五个收件箱，却没有时间。**SkimMail** 将*所有*邮箱——Gmail、Outlook 以及任何 IMAP 账户——汇聚到一个极速、键盘优先的阅读体验中，而且**完全由你自己托管**。

没有云端中间商窥探你的邮件，没有按人头收费的订阅，也没有 2 GB 的 Electron 巨型应用。只有一个内嵌精美 React 界面的**单一 Go 二进制文件**，以 SQLite 为存储——用 Docker 或 `apt` 一分钟内即可安装，重新掌控你的电子邮件。

> 🎯 **以"阅读为先"（read-first）为设计理念。** SkimMail 专为高速*分拣与阅读*而生——稍后提醒、置顶、分组、归档，快速清空收件箱。

|  | SkimMail | 普通网页邮箱 | 桌面客户端 |
|---|---|---|---|
| 自托管，数据留在自己手中 | ✅ | ❌ | ⚠️ 仅本地 |
| 所有账户统一收件箱 | ✅ | ❌ | ⚠️ |
| 单个 21 MB 二进制，无需外部服务 | ✅ | — | ❌ |
| 键盘优先的高速分拣（Superhuman 风格） | ✅ | ❌ | ⚠️ |
| **每个账户独立出口 IP** —— 代理 / WireGuard / 中继，在应用里设置 | ✅ | ❌ | ⚠️ 需外部工具 |
| 免费的 Community Edition + 付费 Pro 层级 | ✅ | 视情况 | 视情况 |

### 🚀 功能特性

**📥 随时随地，尽览所有邮件**
- **多账户 IMAP** ——通过主机/端口 + 应用专用密码添加任意邮件服务商。
- **Gmail 和 Outlook OAuth**（基于 IMAP 的 XOAUTH2）——可选，需使用你自己的 Google/Azure 应用凭据。
- **统一收件箱**汇聚所有账户，并提供轻量级**会话串联（threading）**。
- **三栏界面**，阅读窗格**全面沙箱隔离**——HTML 经过净化，远程图片默认拦截。
- **账户分组**——将账户组织为带名称、带颜色标识的分组，一键阅读整组邮件。

**⚡ 实时推送与搜索**
- **新邮件即时推送**：IMAP **IDLE → WebSocket**，并自动降级为轮询兜底，即使 IDLE 掉线邮件也照常送达。
- **常驻后台同步**——即使没有打开任何浏览器，服务端也持续同步。
- **双层搜索**——毫秒级本地全文搜索（SQLite FTS5），并可选回退到邮件服务器搜索，覆盖缓存窗口之外的邮件。

**🧰 高效阅读利器**
- **稍后提醒（Snooze）** · **置顶（Pin）** · 分流收件箱**分组（Bundles）**（主要 / 简报 / 交易 / 通知）。
- **多选 + 批量操作**（已读 / 归档 / 删除，支持**撤销**）。
- **VIP** 与**静音（Mute）**发件人 · 一键**退订**（RFC 8058）。
- **键盘优先**（Superhuman 风格快捷键，按 `?` 查看）· **专注/分拣模式**（"清零收件箱"）· **命令面板**（⌘K）。

**🕸️ 每个账户都有自己的出口** —— *别的邮件客户端留给你自己解决的那部分*
- **按账户出口（egress）。** 让每个账户的邮件各走各的路：**SOCKS5 代理**、**HTTP 代理**、**内嵌 WireGuard 隧道**，或你自己部署的**无服务器中继**——把一个文件粘贴到 Cloudflare Workers，或在容器里运行 `skimmail relay`。工作与私人邮箱不必共用同一个出口 IP。
- **不需要外部管道。** 邮件客户端通常把这件事交给 `wireproxy`、按应用 VPN 或再起一个容器。在这里，它只是账户上的一个下拉框。
- **是证据，不只是配置。** 每个连接都会报告它**实际离开时的公网 IP** —— 代理、隧道、中继乃至 Direct 都一样 —— 并且当两个本应彼此隔离的账户从同一个地址出现时会**发出警告**。**Test** 会通过该出口建立一次真实连接并报告延迟。
- **失败即关闭。** 关掉一个连接，它的账户会变成 **degraded** 并停止同步 —— 绝不会被悄悄退回到你家里的 IP。

**🤖 规则、告警与运维**
- **Rules & Signals（规则与信号）** —— 按发件人、主题（纯文本或正则）、账户、分组，或“看起来像一次性验证码”匹配，然后通知某个渠道、应用内弹出提示、发送推送，或打上可点击筛选的**标签**。含验证码的邮件会显示 **OTP** 小标签，点一下即可复制；验证码本身从不存储。
- **六种通知渠道** —— 普通 **webhook**、**Telegram**、**Slack**、**Discord**、**Microsoft Teams**、**Google Chat** —— 每个都有自己的 **Send test**，另有发往浏览器和手机的 **Web Push**（标准 VAPID 密钥由 SkimMail 自行生成，无需 Firebase 账号），支持免打扰时段与按账户开关。
- **同步健康** —— 反复失败的账户会**自行停止**，而不是一直冲击你的邮件服务商；看板按 Stopped / Degraded / Paused 展示，并提供一键 Resume、Retry、Stop；当同步停止、代理或隧道掉线、OAuth 登录即将过期时会发出告警。
- **备份与恢复** —— 整个实例的加密快照，可恢复到任意一台 SkimMail 服务器，哪怕它用的是另一种数据库；还可按计划备份到本地目录或任意 S3 兼容存储（MinIO、Cloudflare R2、Backblaze B2、Wasabi、AWS S3）。
- **无需端口转发的远程访问** —— 通过 **Cloudflare Tunnel** 或 **Tailscale** 把 SkimMail 暴露出去，并提供手机扫码。这些程序和 WireGuard 引擎都是**按需下载的插件**，对照一份已签名、按 SHA256 固定的索引校验；在 **Settings ▸ 插件**里安装、更新和删除。
- **在浏览器里配置** —— 公开 URL、OAuth 应用与登录模式都是应用内的设置；用环境变量设定的值依然优先，并显示为锁定且写明变量名。**应用内自更新**可在二进制 / apt 安装上一键装好新版本。

**🌍 平台体验**
- **PWA** ——可从浏览器安装；应用外壳支持离线使用。
- **多语言** —— English · Tiếng Việt · 中文，深色与浅色主题。
- **只有一个软件包**：单个 `skimmail` 二进制 —— **磁盘占用 21 MB，下载 8.7 MB**（在已发布的 v1.11.1 `linux/amd64` 构建上实测）。WireGuard 出口、PostgreSQL、MySQL 和 S3 都已编译进去；WireGuard 引擎在你第一次添加隧道时作为按 SHA256 固定的插件下载。

**🔐 安全与多用户**
- 凭据、OAuth 令牌**以及缓存的邮件正文**均**静态加密存储（AES-256-GCM）**。
- **远程图片默认一直拦截，直到你主动要求** —— 而且那时是 **SkimMail 去取图，不是你的浏览器**，并且走该账户自己的出口，所以发件人的服务器看不到你。
- 首次登录由**一次性认领码**把关，先访问到端口的人无法抢先认领你的实例。
- **真正的多用户** —— `AUTH_MODE=users` 让每个人拥有自己的账户、邮件和设置，别人完全看不到，角色为 **owner**、**operator** 或 **viewer**。
- 登录**限速 + 锁定**、**2FA**、由服务端强制的空闲锁屏、**CSRF/来源校验**、**SSRF 防护**、**已签名的发行版与插件索引**，以及**仅追加的审计日志**。
- 完整安全说明与漏洞报告方式：[`SECURITY.md`](SECURITY.md)。

> 🧭 **以上功能均已发布。** **AI Triage 是唯一仍在路线图上的重要功能** —— 何时上线了什么，见 [changelog](https://github.com/lyquyduong/skimmail/releases)。

### 📦 安装

唯一的前置条件是 **Docker**（或使用 `apt` 的 Ubuntu/Debian）。SQLite 已内置——**无需任何外部服务**。

#### 🐳 Docker

```bash
docker run -d --name skimmail -p 8080:8080 -v skimmail-data:/data \
  -e AUTH_MODE=passphrase ghcr.io/lyquyduong/skimmail:latest
```

#### 🧩 Docker Compose

```bash
curl -fsSLO https://lyquyduong.github.io/skimmail/docker-compose.yml
docker compose up -d      # → http://localhost:8080
```

#### 📦 APT（Ubuntu / Debian）

```bash
curl -fsSL https://lyquyduong.github.io/skimmail/apt/skimmail.gpg \
  | sudo tee /usr/share/keyrings/skimmail.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/skimmail.gpg] https://lyquyduong.github.io/skimmail/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/skimmail.list
sudo apt update && sudo apt install skimmail
sudo systemctl status skimmail                 # → http://localhost:8080
```

以 systemd 服务方式运行。配置：`/etc/default/skimmail` · 数据：`/var/lib/skimmail` · 日志：`/var/log/skimmail/skimmail.log` + `journalctl -u skimmail`（级别/轮转/实时查看见 **Settings ▸ Logs**）· 升级：`sudo apt upgrade`。内嵌 WireGuard 出口就在这一个包里 —— 启用时引擎会作为按 SHA256 固定的插件下载。

#### 🔑 首次登录

打开 **http://localhost:8080**。默认为 `passphrase` 模式：**你输入的第一个密码将成为主密码（master passphrase）**——请务必牢记。用户名留空即可。随后添加 IMAP 账户（邮箱 · 主机 · 端口 `993` · TLS · **应用专用密码**），点击 **↻ Sync** 同步。

> 完整配置参考（OAuth、反向代理、同步调优、全部环境变量）：**[wiki ▸ 配置](https://github.com/lyquyduong/skimmail/wiki/Configuration-zh)**。

#### 🗑️ 卸载（完全移除）

卸载不会自动删除数据（账户、邮件缓存、加密主密钥），除非你一并删除 —— 标 ⚠ 的步骤不可恢复。两个版本命令相同。

```bash
# Docker Compose
docker compose down -v && rm docker-compose.yml                  # ⚠ 容器 + 数据卷

# Docker（docker run）
docker rm -f skimmail && docker volume rm skimmail-data          # ⚠ 全部数据

# APT（Ubuntu / Debian）
sudo apt purge skimmail
sudo rm -rf /var/lib/skimmail /var/log/skimmail && sudo userdel skimmail   # ⚠ 数据 + 日志 + 服务用户
sudo rm -f /etc/apt/sources.list.d/skimmail.list /usr/share/keyrings/skimmail.gpg && sudo apt update
```

> 分步详细说明（包括删除镜像 / 手动二进制安装的卸载）：见 **[wiki ▸ 安装](https://github.com/lyquyduong/skimmail/wiki/Installation-zh)** 的**卸载**章节。

### 📄 许可证

SkimMail 是专有软件，采用 **[Business Source License 1.1](LICENSE)** 许可（在 2030 年 7 月 23 日之前为专有；之后转为 AGPL-3.0+）。

- **Community Edition** —— 免费，无需许可证密钥；10 个账户 / 1 个登录账号 / 3 个代理 / 2 个分组 / 5 GB 存储
- **Sponsor Edition** —— 面向赞助者的答谢（GitHub Sponsors、Open Collective、Ko-fi、直接捐赠）；25 个账户 / 3 个登录账号 / 5 个代理 / 5 个分组 / 15 GB
- **Pro Edition** —— 付费许可证密钥；无限制 + 未来的 AI Triage 服务

许可证本身见 [`LICENSE`](LICENSE)；Sponsor 与 Pro 密钥的商业条款请通过 <skimmail@base101.app> 索取。
- 所有贡献均需签署**[贡献者许可协议（CLA）](CLA.md)**，授予维护者在当前专有许可证及未来任何许可证（包括 2030 年后的 AGPL）下使用你的贡献的权利。

### 🤝 参与贡献

我们非常欢迎你一起让 SkimMail 变得更好！

1. **🐛 缺陷与建议** ——请在 **[github.com/lyquyduong/skimmail/issues](https://github.com/lyquyduong/skimmail/issues)** 提交 issue，并注明版本（`skimmail --version` 或镜像标签）、平台及复现步骤。
2. **🔧 代码贡献** ——主开发仓库目前为私有；动手写代码之前，请**先发邮件联系维护者**：**skimmail@base101.app**。所有贡献均需签署 [CLA](CLA.md)。
3. **🔒 安全漏洞** ——请**不要**提交公开 issue；请按照 [`SECURITY.md`](SECURITY.md) 的流程报告。
4. **🌐 翻译与文档** ——欢迎改进 EN/VI/ZH 语言文案与各类文档。

开发者指南（源码构建、热重载、测试）会在签署 CLA 后提供给贡献者 —— 请从上面第 2 步开始。

### ❤️ 赞助支持

如果 SkimMail 每天都在为你节省时间，欢迎支持项目的持续开发：

| 渠道 | 链接 |
|---|---|
| 💖 GitHub Sponsors | <!-- TODO: 添加链接 --> *即将开通* |
| ☕ Buy Me a Coffee | <!-- TODO: 添加链接 --> *即将开通* |
| ₿ 加密货币（BTC / ETH / USDT） | <!-- TODO: 添加钱包地址 --> *即将开通* |

⭐ **为 [GitHub 仓库](https://github.com/lyquyduong/skimmail) 点亮 Star——零成本，大鼓励！**

---

<div align="center">

**SkimMail** — *Skim all your inboxes.* 📬

Made with ☕ and Go · [Releases](https://github.com/lyquyduong/skimmail/releases) · [Documentation](https://github.com/lyquyduong/skimmail/wiki) · [Issues](https://github.com/lyquyduong/skimmail/issues)

</div>
