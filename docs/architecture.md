# Architecture

---

## Overview

Px is a lightweight HTTP/HTTPS proxy server that enables applications to authenticate through NTLM or Kerberos proxy servers without handling the complex handshake themselves. It leverages Windows SSPI (or equivalent mechanisms on other OSes) to perform single-sign-on using the currently logged-in user credentials. Px runs on Windows, Linux and macOS, either as a Python module installed via `pip` or as a compiled binary built with Nuitka.

## Runtime model

The runtime consists of a master process that spawns worker processes (configurable via `--workers`). Each worker creates a thread pool (`--threads`) and runs a `ThreadedTCPServer`. Incoming connections are handled by `PxHandler` (subclass of `http.server.BaseHTTPRequestHandler`). Requests are forwarded to libcurl (`mcurl.Curl`) which performs the actual HTTP exchange, optionally authenticating to the upstream proxy.

```
Client ─── HTTP request ──► PxHandler.do_curl
                              │
                    Select proxy via px.wproxy.Wproxy
                              │
                    Forward via mcurl.Curl ──► Upstream proxy
                              │
                         Response ◄── Upstream proxy
                              │
                    Return to client
```

## Package layout

| File | Responsibility |
|------|----------------|
| `px/main.py` | Entry point, multiprocessing, TCP server setup |
| `px/handler.py` | `PxHandler` — request handling, client auth, curl integration |
| `px/config.py` | `State` singleton, CLI/env/INI parsing, proxy reload |
| `px/wproxy.py` | `Wproxy` — proxy discovery from config, environment, Windows Internet Options |
| `px/pac.py` | `Pac` — PAC file loading and evaluation via quickjs-ng |
| `px/pacutils.py` | Mozilla PAC utility functions (JavaScript) |
| `px/debug.py` | `Debug` — stdout/file logging redirection |
| `px/help.py` | CLI help text |
| `px/version.py` | Version string |
| `px/windows.py` | Windows-specific: registry install, console attach/detach |

## Authentication

Px can authenticate to the upstream proxy using:
- **SSPI / GSS-API** when available (Windows default).
- **Username / password** supplied via `--username` and stored in the system keyring (`keyring` module) under the realm `Px`.
- **Explicit `--auth=NONE`** to defer authentication to the client.

Downstream client authentication (gateway mode) is optional and supports `NEGOTIATE`, `NTLM`, `DIGEST`, and `BASIC`. Credentials are retrieved from the keyring under the realm `PxClient` or from environment variables `PX_CLIENT_PASSWORD` / `PX_CLIENT_USERNAME`.

## Proxy discovery (`px.wproxy`)

The `Wproxy` class abstracts proxy information from several sources:
- **Configuration** (`--proxy` / `--pac`) — mode `MODE_CONFIG` or `MODE_CONFIG_PAC`.
- **Operating-system settings** — Windows Internet Options (auto-detect, PAC, manual), environment variables (`http_proxy`, `https_proxy`, `no_proxy`).
- **Direct** — when no proxy is defined.

It also parses the `noproxy` list, supporting IP ranges, CIDR, wildcards, and hostnames. The method `find_proxy_for_url(url)` returns a list of proxy servers or `DIRECT`.
