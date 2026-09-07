<div align="center">
  <img src="https://raw.githubusercontent.com/INAPP-Mobile/flaresolverr/main/template-icon.svg" alt="FlareSolverr" width="120" height="120">
  <h1 align="center">FlareSolverr</h1>
  <p align="center">Proxy server to bypass Cloudflare and DDoS-GUARD protection.</p>
</div>

<p align="center">
  <a href="https://railway.com/new/template/flaresolverr"><img src="https://railway.app/button.svg" alt="Deploy on Railway"></a>
  <a href="https://github.com/FlareSolverr/FlareSolverr"><img src="https://img.shields.io/github/stars/FlareSolverr/FlareSolverr?style=for-the-badge&logo=github&label=GitHub" alt="GitHub Stars"></a>
</p>

---

## ✨ Features

- **Cloudflare Bypass** — Solves Cloudflare challenges via headless Chromium
- **DDoS-GUARD Bypass** — Handles DDoS-GUARD protected sites
- **Proxy Server** — REST API endpoint at `/v1` for programmatic requests
- **Session Management** — Persistent sessions for authenticated scraping
- **Cookie Return** — Returns solved cookies for reuse in other HTTP clients
- **Lightweight** — Runs on a single Railway container

## 🚀 Deploy on Railway

### One-Click Deploy

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.com/new/template/flaresolverr)

Click the button above to deploy FlareSolverr on Railway.

### After Deploy

1. Copy your service URL (e.g. `https://your-service.up.railway.app`)
2. Send POST requests to `/v1` endpoint:

```bash
curl -X POST 'https://your-service.up.railway.app/v1' \
  -H 'Content-Type: application/json' \
  -d '{
    "cmd": "request.get",
    "url": "https://example.com",
    "maxTimeout": 60000
  }'
```

## 📖 Usage

FlareSolverr exposes a single `/v1` endpoint. All requests are JSON POST.

### Commands

| Command | Description |
|---------|-------------|
| `request.get` | GET request through FlareSolverr |
| `request.post` | POST request through FlareSolverr |
| `sessions.create` | Create a persistent session |
| `sessions.list` | List active sessions |
| `sessions.destroy` | Destroy a session |

### Example: GET Request

```json
{
  "cmd": "request.get",
  "url": "https://www.example.com",
  "maxTimeout": 60000
}
```

### Example: POST Request

```json
{
  "cmd": "request.post",
  "url": "https://www.example.com/api",
  "postData": "param1=value1&param2=value2",
  "maxTimeout": 60000
}
```

### Example: Session

```json
{
  "cmd": "sessions.create",
  "session": "my-session-id"
}
```

Then reuse the session in subsequent requests:

```json
{
  "cmd": "request.get",
  "url": "https://www.example.com",
  "session": "my-session-id"
}
```

## 🔧 Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `PORT` | Yes | FlareSolverr proxy port. Railway injects PORT for routing. Default: 8191 |
| `LOG_LEVEL` | No | Log level: debug, info, warn, error. Default: info |
| `LOG_FILE` | No | Log file path. Empty for stdout only. |
| `LOG_HTML` | No | Log full HTML responses (debug only — verbose). Default: false |
| `CAPTCHA_SOLVER` | No | Captcha solver provider. 'none' for Cloudflare-only challenges. Default: none |
| `TZ` | No | Timezone for log timestamps. Default: UTC |
| `PROXY_URL` | No | Upstream HTTP/SOCKS proxy URL (e.g. http://proxy:8080). Leave empty to disable. |
| `PROXY_USERNAME` | No | Proxy username. Only used with PROXY_URL. |
| `PROXY_PASSWORD` | No | Proxy password. Only used with PROXY_URL. |

## ⚠️ Security Notice

**DO NOT expose FlareSolverr to the public internet without authentication.** It can be abused for malicious scraping. Railway's public endpoint is protected, but if you add a custom domain, ensure you also add authentication (e.g. a Railway private service or a reverse proxy with Basic Auth).

## 📄 License

This template deploys [FlareSolverr](https://github.com/FlareSolverr/FlareSolverr), licensed under MIT.
