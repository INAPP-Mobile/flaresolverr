# Deploy and Host

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.com/new/template/flaresolverr)

![FlareSolverr](https://raw.githubusercontent.com/INAPP-Mobile/flaresolverr/main/template-icon.svg)

FlareSolverr is a proxy server to bypass Cloudflare and DDoS-GUARD protection. It uses headless Chromium to solve challenges and returns the HTML and cookies so other HTTP clients can access protected sites.

## About Hosting

The template deploys a single FlareSolverr service from the official `ghcr.io/flaresolverr/flaresolverr:v3.5.0` image:

- **flaresolverr** — Bottle-based proxy server with `/v1` API endpoint and `/health` check, `PORT=8191`

Railway provides compute, TLS at the edge, and a public URL. The service runs headless Chromium via `undetected-chromedriver` inside the container, so no additional browser installation is needed.

## Why Deploy

- **Cloudflare bypass** — Solve Cloudflare IUAM/IUAM-v2 and Turnstile challenges automatically
- **DDoS-GUARD support** — Handle DDoS-GUARD protected sites as well
- **Session management** — Create persistent sessions to reuse solved challenges across requests
- **Cookie return** — Returns solved cookies for reuse in other HTTP clients
- **Single container** — No database, no queue, no extra services — just one lightweight proxy

## Common Use Cases

- **Web scraping** — Access sites that block datacenter IPs with Cloudflare challenges
- **API integration** — Integrate with scrapers that need Cloudflare bypass (e.g. Sonarr, Readarr, Prowlarr)
- **Research** — Access protected content for legitimate research purposes
- **Testing** — Test applications behind Cloudflare in CI/CD pipelines

## Dependencies for FlareSolverr

FlareSolverr has no external service dependencies — it runs as a single container.

### Deployment Dependencies

- **No external database required** — All state is in-memory or per-request
- **No external cache required** — Session state is managed in the service itself
- **No storage volume required** — The service is stateless

Optional: if you want to use an upstream proxy, set `PROXY_URL` to route requests through your own HTTP/SOCKS proxy.

## Usage

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

```bash
curl -X POST 'https://your-service.up.railway.app/v1' \
  -H 'Content-Type: application/json' \
  -d '{
    "cmd": "request.get",
    "url": "https://www.example.com",
    "maxTimeout": 60000
  }'
```

### Example: Session

```bash
curl -X POST 'https://your-service.up.railway.app/v1' \
  -H 'Content-Type: application/json' \
  -d '{
    "cmd": "sessions.create",
    "session": "my-session-id"
  }'
```

Then reuse the session:

```bash
curl -X POST 'https://your-service.up.railway.app/v1' \
  -H 'Content-Type: application/json' \
  -d '{
    "cmd": "request.get",
    "url": "https://www.example.com",
    "session": "my-session-id"
  }'
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `PORT` | Yes | FlareSolverr proxy port. Railway injects PORT for routing. Default: 8191 |
| `LOG_LEVEL` | No | Log level: debug, info, warn, error. Default: info |
| `LOG_FILE` | No | Log file path. Empty for stdout only. |
| `LOG_HTML` | No | Log full HTML responses (debug only — very verbose). Default: false |
| `CAPTCHA_SOLVER` | No | Captcha solver provider. 'none' for Cloudflare-only challenges. Default: none |
| `TZ` | No | Timezone for log timestamps. Default: UTC |
| `PROXY_URL` | No | Upstream HTTP/SOCKS proxy URL (e.g. http://proxy:8080). Leave empty to disable. |
| `PROXY_USERNAME` | No | Proxy username. Only used with PROXY_URL. |
| `PROXY_PASSWORD` | No | Proxy password. Only used with PROXY_URL. |

## Security Notice

**DO NOT expose FlareSolverr to the public internet without authentication.** It can be abused for malicious scraping. Railway's public endpoint is protected, but if you add a custom domain, ensure you also add authentication (e.g. a Railway private service or a reverse proxy with Basic Auth).

## License

This template deploys [FlareSolverr](https://github.com/FlareSolverr/FlareSolverr), licensed under MIT.
