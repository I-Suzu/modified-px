# Configuration

---

## Overview

Px requires only one piece of information to function — the server name and port
of the proxy server. If not specified, Px will check Internet Options or
environment variables for any proxy definitions. Without this, Px will try to
connect to sites directly.

## Configuration sources

Configuration can be supplied from multiple sources, applied in order of
precedence (highest first):

1. **Command-line flags** (e.g. `--proxy`, `--listen`, `--auth`).
2. **Environment variables** prefixed with `PX_` (e.g. `PX_PROXY`, `PX_PORT`).
3. **Variables in a dotenv file** (`.env`) — in the working directory or Px directory.
4. **Configuration file** `px.ini` — searched in the working directory, user config
   directory, or Px directory.

The `State` singleton (`px.config.STATE`) parses these sources, fills missing values
with defaults defined in `DEFAULTS`, and validates them.

## User config directory

| Platform | Path |
|----------|------|
| Windows | `%APPDATA%\Roaming\px` |
| Linux | `~/.config/px` |
| macOS | `~/Library/Application Support/px` |

## Key options

| Flag | Env var | INI key | Description |
|------|---------|---------|-------------|
| `--proxy=HOST:PORT` | `PX_SERVER` | `proxy:server` | Upstream proxy (comma-separated list) |
| `--pac=URL` | `PX_PAC` | `proxy:pac` | PAC file URL or path |
| `--listen=IP` | `PX_LISTEN` | `proxy:listen` | Local interface(s) to bind (default 127.0.0.1) |
| `--port=NUM` | `PX_PORT` | `proxy:port` | Listening port (default 3128) |
| `--gateway=0\|1` | `PX_GATEWAY` | `proxy:gateway` | Allow remote clients |
| `--hostonly=0\|1` | `PX_HOSTONLY` | `proxy:hostonly` | Restrict to host-only interfaces |
| `--allow=IPGLOB` | `PX_ALLOW` | `proxy:allow` | IPs allowed when gateway is enabled |
| `--noproxy=LIST` | `PX_NOPROXY` | `proxy:noproxy` | Hosts/IPs that bypass the upstream proxy |
| `--auth=TYPE` | `PX_AUTH` | `proxy:auth` | Upstream proxy auth type |
| `--client-auth=TYPE` | `PX_CLIENT_AUTH` | `client:client_auth` | Downstream client auth type |
| `--username=DOMAIN\user` | `PX_USERNAME` | `proxy:username` | Username for upstream auth |
| `--client-username=DOMAIN\user` | `PX_CLIENT_USERNAME` | `client:client_username` | Username for client auth |
| `--workers=N` | `PX_WORKERS` | `settings:workers` | Number of worker processes (default 2) |
| `--threads=N` | `PX_THREADS` | `settings:threads` | Threads per worker (default 32) |
| `--idle=N` | `PX_IDLE` | `settings:idle` | Idle timeout in seconds (default 30) |
| `--socktimeout=N` | `PX_SOCKTIMEOUT` | `settings:socktimeout` | Connection timeout (default 20) |
| `--proxyreload=N` | `PX_PROXYRELOAD` | `settings:proxyreload` | Proxy info refresh interval (default 60) |
| `--log=0\|1\|2\|3\|4` | `PX_LOG` | `settings:log` | Logging destination |

## Credentials

If SSPI is not available, `--username` in `domain\username` format allows Px to
authenticate as that user. The password is retrieved using Python keyring under the
realm `Px`.

Credentials can be set with:

```bash
px --username=domain\username --password
```

`PX_PASSWORD` and `PX_CLIENT_PASSWORD` environment variables are available as
alternatives when keyring is not available.

## Saving configuration

The `--save` flag writes the current configuration to `px.ini`. Px will save to
the first writable location found among the working directory, user config
directory, or script directory.

## Auth types

Supported values for `--auth`:
- `ANY`, `NTLM`, `NEGOTIATE`, `DIGEST`, `BASIC` — standard auth methods.
- Prefix `NO` to exclude a method (e.g. `NONTLM`).
- Prefix `SAFENO` to exclude from ANYSAFE (e.g. `SAFENONTLM`).
- Prefix `ONLY` to allow only that method (e.g. `ONLYNTLM`).
- `NONE` — defer all authentication to the client.
