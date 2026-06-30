# SkimMail

[English](README.md) · **Tiếng Việt** · [中文](README.zh.md)

> Lướt nhanh mọi hộp thư. Trình đọc email **đọc-trước (read-first)**, đa tài khoản,
> tự host, nhẹ — lõi IMAP + OAuth Gmail/Outlook — đóng gói thành **một file binary
> Go duy nhất** nhúng sẵn React SPA. Mặc định **không cần dịch vụ ngoài**.

**Giấy phép:** AGPL-3.0-or-later (+ CLA, xem [`CLA.md`](CLA.md)) · **Bảo mật:** [`SECURITY.md`](SECURITY.md)

---

## Tính năng

- **Đọc IMAP đa tài khoản** — thêm account bằng host/port + app-password.
- **OAuth** cho Gmail & Outlook (XOAUTH2 qua IMAP) — *tùy chọn, cần app credentials
  Google/Azure của riêng bạn*.
- **Hộp thư hợp nhất** xuyên mọi tài khoản, **gom luồng (threading)** nhẹ, **giao
  diện 3 khung** với khung đọc cô lập (sanitize HTML, chặn ảnh từ xa).
- **Thời gian thực**: mail mới đẩy qua IMAP **IDLE → WebSocket**, kèm **poll dự
  phòng** để mail vẫn về nếu IDLE rớt — backend đồng bộ cả khi không có client mở.
- **Tìm kiếm 2 tầng** — full-text cục bộ nhanh (SQLite FTS5) + tùy chọn **fallback
  phía server** để tìm cả mail ngoài phạm vi đã cache.
- **Đọc nhanh** — tạm ẩn (snooze), ghim (pin), **bundles** chia hộp thư, chọn nhiều
  + thao tác hàng loạt, sender **VIP / mute**, **hủy đăng ký 1 chạm** (RFC 8058).
- **AI (tùy chọn, dùng key của bạn)** — tóm tắt, phân loại, trích action, dịch — qua
  **Claude / OpenAI-compatible / Ollama**. Mặc định tắt.
- **Bàn phím trước hết** (kiểu Superhuman) + **chế độ Focus/triage** + **PWA** (cài
  được, chạy offline phần vỏ) + **đa ngôn ngữ** (English / Tiếng Việt / 中文, giao
  diện sáng & tối).
- **Lưu trữ cắm được (nâng cao)** — mặc định **SQLite + filesystem**; tùy chọn
  **Postgres/MySQL** và blob **tương thích S3** qua build tag.
- **Bộ điều phối sync** (đồng thời + giới hạn nhịp + hàng đợi ưu tiên) và lớp **bảo
  mật**: rate-limit + lockout đăng nhập, kiểm tra CSRF/Origin, chống SSRF, mã hoá
  credential at-rest, và **audit log**.

## Yêu cầu

Chỉ cần **Docker** — SQLite tích hợp sẵn, không cần dịch vụ ngoài.
*(Build từ mã nguồn hoặc đóng góp? Xem [`README.dev.md`](README.dev.md).)*

---

## Bắt đầu nhanh

```bash
docker compose up --build          # → http://localhost:8080  (SQLite + filesystem)
```

Thử **stack stateless** (Postgres + S3/MinIO) bằng profile `advanced`:

```bash
docker compose --profile advanced up --build
# → skimmail:      http://localhost:8081
# → MinIO console: http://localhost:9001  (minioadmin / minioadmin)
docker compose --profile advanced down -v   # dừng + xoá volume
```

Hoặc chạy image thuần (distroless + non-root; dữ liệu ở volume `/data`):

```bash
docker build -t skimmail .
docker run -p 8080:8080 -v skimmail-data:/data skimmail
```

### Đăng nhập lần đầu

Mở **http://localhost:8080**. Chế độ auth mặc định là `passphrase`: **mật khẩu bạn
nhập lần đầu tiên sẽ trở thành passphrase chính** — hãy nhớ nó (dùng để đăng nhập
lại). Để trống ô username.

### Thêm tài khoản mail

Thêm tài khoản **IMAP**: email · host (vd `imap.gmail.com`) · port `993` · bảo mật
`TLS` · **app password**.

> Gmail/Outlook yêu cầu **app-password** riêng (không phải mật khẩu thường) trừ khi
> đã cấu hình OAuth. Muốn bật nút "Thêm bằng Google/Microsoft", hãy đặt OAuth
> credentials bên dưới + `BASE_URL` công khai.

Bấm **↻ Sync** (hoặc đợi poll nền). Sau đó mail mới đẩy về thời gian thực qua WebSocket.

---

## Sử dụng

- **Tìm kiếm** — gõ vào ô search (Enter). Khi chọn 1 account, nút **"Search server ↗"**
  còn tìm trên máy chủ mail và cache kết quả.
- **Bundles** — lọc danh sách theo Primary / Newsletter / Transactional /
  Notifications. **💤 Snoozed** xem mail đang tạm ẩn.
- **Chọn nhiều** — tick các dòng để **bulk** read / archive / delete.
- **Khung đọc** — 🗄 Archive · 🗑 Delete (có **Undo**) · 💤 Snooze · 📌 Pin ·
  ✨ Summarize · ⚡ Actions · 🌐 Translate · ✉️ Unsubscribe · ★ VIP · 🔇 Mute.
- **AI** — mở **🤖 AI settings**, bật lên, chọn vendor + model + key (hoặc dùng
  **Ollama** local không cần key). Nội dung mail sẽ gửi tới vendor bạn chọn.
- **Bàn phím** — `?` hiện toàn bộ phím tắt: `j`/`k` di chuyển · `Enter`/`o` mở ·
  `e` archive · `#` delete · `s` snooze · `x` chọn · `Space` xem nhanh ·
  `/` tìm kiếm · **`f` chế độ Focus** ("Get to Zero") · `Esc` đóng.
- **PWA** — cài từ thanh địa chỉ trình duyệt; phần vỏ app chạy offline.
- **Ngôn ngữ / giao diện** — đổi EN / VI / 中 và sáng/tối ở sidebar.

---

## Cấu hình

Cấu hình qua biến môi trường (12-factor). Xem [`.env.example`](.env.example).

| Biến | Mặc định | Mục đích |
|---|---|---|
| `AUTH_MODE` | `passphrase` | `passphrase` (1 user) hoặc `users` (đa user) |
| `DATA_DIR` | `./data` | SQLite DB, master key, blobs |
| `LISTEN_ADDR` | `:8080` | địa chỉ lắng nghe |
| `BASE_URL` | – | URL công khai — **bắt buộc** cho OAuth redirect, WebSocket, PWA |
| `TRUST_PROXY` | `false` | tin `X-Forwarded-*` sau reverse proxy |
| `TRUSTED_ORIGINS` | – | origin được phép thêm cho CORS/CSRF (phân tách dấu phẩy) |
| `BACKGROUND_POLL_INTERVAL` | `5m` | chu kỳ poll dự phòng cho mọi account |
| `MAX_CONCURRENT_SYNCS` | `4` | số mailbox đồng bộ cùng lúc tối đa (scheduler) |
| `SYNC_RATE_PER_MIN` | `10` | số lượt sync khởi động mỗi phút (stagger) |
| `SYNC_DEPTH_DAYS` | `30` | cửa sổ sync header (0 = toàn bộ lịch sử) |
| `GOOGLE_CLIENT_ID` / `_SECRET` | – | OAuth app Gmail (tùy chọn) |
| `MICROSOFT_CLIENT_ID` / `_SECRET` | – | OAuth app Outlook (tùy chọn) |
| `DB_DRIVER` | `sqlite` | `sqlite` \| `postgres` \| `mysql` (nâng cao) |
| `DATABASE_URL` | – | DSN khi `DB_DRIVER` ≠ sqlite |
| `BLOB_STORE` | `fs` | `fs` \| `s3` (nâng cao) |
| `S3_ENDPOINT` / `S3_BUCKET` / `S3_REGION` / `S3_ACCESS_KEY` / `S3_SECRET_KEY` / `S3_FORCE_PATH_STYLE` | – | blob store tương thích S3 |

### Nâng cao: Postgres / MySQL / S3

Image mặc định là SQLite + filesystem cục bộ. Muốn chạy trên **Postgres/MySQL** với
blob **tương thích S3**, dùng profile **`advanced`** của Compose (ở trên), hoặc build
image riêng có backend biên dịch sẵn:

```bash
docker build --build-arg TAGS="postgres s3" -t skimmail:advanced .
```

Sau đó đặt `DB_DRIVER` / `DATABASE_URL` / `BLOB_STORE` / `S3_*` theo bảng trên (DSN
MySQL cần `multiStatements=true`). Chi tiết build từ nguồn: [`README.dev.md`](README.dev.md).

---

## Bảo mật

Credential và token AI/OAuth được mã hoá at-rest (AES-256-GCM). Server áp
rate-limit + lockout đăng nhập, kiểm tra CSRF/Origin, chống SSRF cho fetch ra
ngoài, security headers nghiêm ngặt + iframe email cô lập, và audit log append-only
(`GET /api/audit`). Xem [`SECURITY.md`](SECURITY.md) để biết tư thế bảo mật đầy đủ
và cách báo lỗ hổng.

## Cho lập trình viên

Build từ mã nguồn, chế độ dev (hot reload), thiết lập VS Code, build tags, tests và
bố cục dự án nằm ở **[`README.dev.md`](README.dev.md)**.

## Giấy phép

AGPL-3.0-or-later. Đóng góp cần ký [CLA](CLA.md). SPDX: `AGPL-3.0-or-later`.
