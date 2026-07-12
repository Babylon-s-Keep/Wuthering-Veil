# Installing Wuthering Veil (from Releases)

Download files from:  
**https://github.com/badrust0/Wuthering-Veil/releases**

Do **not** use “Source code (zip)” unless you are a developer with a private source license.

---

## Linux agent (available now)

**File:** `wv-agent-1.0.0-linux-amd64.tar.gz`

**On each Linux host you want to monitor:**

```bash
tar xzf wv-agent-1.0.0-linux-amd64.tar.gz
cd wv-agent-1.0.0-linux-amd64

WV_SERVER_URL=https://YOUR_WV_SERVER \
WV_API_KEY=YOUR_AGENT_KEY \
  sudo ./install.sh
```

| Variable | Description |
|----------|-------------|
| `WV_SERVER_URL` | Your Wuthering Veil server URL (no trailing slash) |
| `WV_API_KEY` | Agent key — same as configured on the server |

**Verify:**

```bash
systemctl status wv-agent
journalctl -u wv-agent -f
```

**Plugins (optional):** place `.so` files in `/etc/wv/plugins/` and restart the agent.

---

## Control plane — server + web dashboard

**Not yet available as a public binary download.**

When published, installation will be via Docker images (no source build required). Check Releases for:

- `wv-server` / `wv-web` Docker images, or
- a one-line `docker compose` bundle

Contact your administrator for server URL and agent key until then.

---

## Desktop app

**Coming soon** on Releases as `.deb` / AppImage for Linux administrators.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `ingest failed (HTTP 401)` | Wrong `WV_API_KEY` |
| `ingest failed (HTTP 0)` | Server unreachable, TLS, or firewall |
| Agent not in dashboard | Check server URL; wait ~30s for first poll |

---

**Version:** see [VERSION](VERSION) in this repository.
