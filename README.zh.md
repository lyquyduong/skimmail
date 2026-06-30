# SkimMail

[English](README.md) · [Tiếng Việt](README.vi.md) · **中文**

> 速览你的所有收件箱。一个轻量、自托管、**以阅读为先**的多账户邮件阅读器——以
> IMAP 为核心 + Gmail/Outlook OAuth——打包为**单个 Go 二进制**并内嵌 React SPA。
> **无需任何外部服务**。

**许可证：** AGPL-3.0-or-later（+ CLA，见 [`CLA.md`](CLA.md)） · **安全：** [`SECURITY.md`](SECURITY.md)

> **这是 `0.1.0` 版本** —— 专注的邮件阅读器 + 账户分组。其余能力（AI、通知、可插拔
> 存储、应用内更新）已列入路线图。

---

## 功能

- **多账户 IMAP** 阅读——通过 host/port + 应用专用密码添加账户。
- **OAuth** 支持 Gmail 与 Outlook（基于 IMAP 的 XOAUTH2）——*可选，需要你自己的
  Google/Azure 应用凭据*。
- 跨所有账户的**统一收件箱**、轻量**会话线程（threading）**、**三栏界面**，阅读
  区在沙箱 iframe 中渲染（HTML 净化、默认拦截远程图片）。
- **账户分组**——把多个账户归入有名称、带配色的分组，按组一起阅读邮件。
- **实时**新邮件推送：IMAP **IDLE → WebSocket**，并带**轮询兜底**，即使 IDLE 断开
  也能收到邮件——即便没有客户端打开，后端也会持续同步。
- **两级搜索**——本地快速全文（SQLite FTS5）+ 可选**服务端回退**，触达缓存窗口之外
  的邮件。
- **快速阅读**工具——稍后处理（snooze）、置顶（pin）、分类收件箱 **bundles**、多选 +
  批量操作、**VIP / 静音**发件人、一键**退订**（RFC 8058）。
- **键盘优先**（Superhuman 风格）+ **专注/处理模式** + **命令面板**（⌘K）+ **PWA**
  （可安装、外壳离线可用）+ **多语言**（English / Tiếng Việt / 中文，深色与浅色主题）。
- **常驻后台同步**与**安全**层：登录限流 + 锁定、CSRF/Origin 校验、SSRF 防护、凭据
  静态加密、以及审计日志。

## 环境要求

只需 **Docker** —— 内置 SQLite，无需任何外部服务。
*（从源码构建或参与贡献？见 [`README.dev.md`](README.dev.md)。）*

---

## 快速开始

```bash
docker compose up --build          # → http://localhost:8080（SQLite + 文件系统）
```

或运行纯镜像（distroless + 非 root；数据在 `/data` 卷）：

```bash
docker run -d -p 8080:8080 -v skimmail-data:/data \
  -e AUTH_MODE=passphrase ghcr.io/lyquyduong/skimmail:latest
```

### 在 Ubuntu / Debian 上安装（apt）

```bash
curl -fsSL https://lyquyduong.github.io/skimmail/apt/skimmail.gpg \
  | sudo tee /usr/share/keyrings/skimmail.gpg >/dev/null
echo "deb [signed-by=/usr/share/keyrings/skimmail.gpg] https://lyquyduong.github.io/skimmail/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/skimmail.list
sudo apt update && sudo apt install skimmail      # 作为 systemd 服务运行 → http://localhost:8080
```

配置位于 `/etc/default/skimmail`；升级通过 `apt upgrade`。所有发布渠道（镜像、`.deb`、
apt 仓库）都从 GitHub 发布——见 [`DISTRIBUTION.md`](DISTRIBUTION.md)。

### 首次登录

打开 **http://localhost:8080**。默认认证模式为 `passphrase`：**你第一次输入的密码会
成为主口令** ——请牢记（之后用它登录）。用户名留空。

### 添加邮箱账户

添加 **IMAP** 账户：邮箱 · host（如 `imap.gmail.com`） · 端口 `993` · 安全 `TLS` ·
**应用专用密码**。

> 除非已配置 OAuth，否则 Gmail/Outlook 需要**应用专用密码**（不是常规密码）。要启用
> “使用 Google/Microsoft 添加”按钮，请配置下方的 OAuth 凭据并设置公开的 `BASE_URL`。

点击 **↻ Sync**（或等待后台轮询）。之后新邮件会通过 WebSocket 实时推送。

---

## 使用

- **搜索**——在搜索框输入（回车）。选中单个账户时，**“Search server ↗”**按钮还会
  查询邮件服务器并缓存命中结果。
- **分组**——在 **Settings → Groups** 创建账户分组；选择某个分组即可只阅读该组账户
  的邮件。
- **Bundles**——按 Primary / Newsletter / Transactional / Notifications 过滤列表。
  **💤 Snoozed** 查看已稍后处理的邮件。
- **多选**——勾选行进行**批量** read / archive / delete。
- **阅读区**——🗄 归档 · 🗑 删除（可 **Undo**） · 💤 稍后 · 📌 置顶 · ✉️ 退订 ·
  ★ VIP · 🔇 静音。
- **键盘**——`?` 显示全部快捷键：`j`/`k` 移动 · `Enter`/`o` 打开 · `e` 归档 ·
  `#` 删除 · `s` 稍后 · `x` 选择 · `Space` 快速预览 · `/` 搜索 ·
  **`f` 专注模式**（“清空收件箱”） · `Esc` 关闭。
- **PWA**——从浏览器地址栏安装；应用外壳可离线使用。
- **语言 / 主题**——在侧栏切换 EN / VI / 中 与深色/浅色。

---

## 配置

通过环境变量配置（12-factor）。见 [`.env.example`](.env.example)。

| 变量 | 默认 | 用途 |
|---|---|---|
| `AUTH_MODE` | `passphrase` | `passphrase`（单用户）或 `users`（多用户） |
| `DATA_DIR` | `./data` | SQLite 数据库、主密钥、blobs |
| `LISTEN_ADDR` | `:8080` | 监听地址 |
| `BASE_URL` | – | 公开 URL——OAuth 回调、WebSocket、PWA **必需** |
| `TRUST_PROXY` | `false` | 反向代理后信任 `X-Forwarded-*` |
| `TRUSTED_ORIGINS` | – | 额外允许的 CORS/CSRF 来源（逗号分隔） |
| `BACKGROUND_POLL_INTERVAL` | `5m` | 每个账户的轮询兜底周期 |
| `MAX_CONCURRENT_SYNCS` | `4` | 同时同步的最大邮箱数 |
| `SYNC_RATE_PER_MIN` | `10` | 每分钟启动同步的次数上限（错峰） |
| `SYNC_DEPTH_DAYS` | `30` | 邮件头同步窗口（0 = 全部历史） |
| `GOOGLE_CLIENT_ID` / `_SECRET` | – | Gmail OAuth 应用（可选） |
| `MICROSOFT_CLIENT_ID` / `_SECRET` | – | Outlook OAuth 应用（可选） |

---

## 安全

凭据与 OAuth 令牌静态加密（AES-256-GCM）。服务端实施登录限流 + 锁定、CSRF/Origin
校验、对外请求的 SSRF 防护、严格安全响应头 + 沙箱邮件 iframe，以及只追加的审计日志
（`GET /api/audit`）。完整安全姿态与漏洞报告方式见 [`SECURITY.md`](SECURITY.md)。

## 面向开发者

从源码构建、开发模式（热重载）、VS Code 配置、build tags、测试与项目结构都在
**[`README.dev.md`](README.dev.md)**。

## 许可证

AGPL-3.0-or-later。贡献需签署 [CLA](CLA.md)。SPDX：`AGPL-3.0-or-later`。
