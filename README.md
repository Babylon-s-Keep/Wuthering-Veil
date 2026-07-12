# Wuthering Veil

Security Operation Center for Linux fleets — monitoring, alerts, and host security signals.

**Download:** [GitHub Releases](https://github.com/badrust0/Wuthering-Veil/releases)

| Component | Status | Get it |
|-----------|--------|--------|
| **Linux agent** (`wv-agent`) | Available v1.0.0 | Release asset: `wv-agent-1.0.0-linux-amd64.tar.gz` |
| **Control plane** (server + dashboard) | Coming soon | Pre-built Docker images |
| **Desktop app** | Coming soon | `.deb` / AppImage on Releases |

---

## Quick install (agent)

See **[INSTALL.md](INSTALL.md)** for full steps.

```bash
# Download wv-agent-1.0.0-linux-amd64.tar.gz from Releases, then:
tar xzf wv-agent-1.0.0-linux-amd64.tar.gz
cd wv-agent-1.0.0-linux-amd64
WV_SERVER_URL=https://your-wv-server \
WV_API_KEY=your-agent-key \
  sudo ./install.sh
```

---

## Support

- **Install guide:** [INSTALL.md](INSTALL.md)
- **Changelog:** [CHANGELOG.md](CHANGELOG.md)
- **Version:** see [VERSION](VERSION)

This repository contains **installation documentation** and **release binaries**. Source code is not published here.
