# Usage

---

## Quick start

```bash
# Run as a simple proxy on localhost
px --proxy=proxyserver.com:8080

# Use a PAC file
px --pac=file:///path/to/config.pac

# Run with verbose logging
px --proxy=proxyserver.com:80 --verbose
```

## Configuration

Px requires only one piece of information to function — the server name and port
of the proxy server. If not specified, Px will check Internet Options or
environment variables for any proxy definitions. Without this, Px will try to
connect to sites directly.

The `noproxy` capability allows Px to connect to configured hosts directly,
bypassing the proxy altogether.

Configuration can be specified in multiple ways, listed in order of precedence:

1. **Command line flags**
2. **Environment variables** prefixed with `PX_`
3. **Variables in a dotenv file** (`.env`) — in the working directory or Px directory
4. **Configuration file** `px.ini` — searched in:
   - Working directory
   - User config directory
     - Windows: `%APPDATA%\Roaming\px`
     - Linux: `~/.config/px`
     - Mac: `~/Library/Application Support/px`
   - Px directory

Refer to `px --help` or see the [configuration reference](configuration.md) for
all options.

### Saving configuration

The `--save` flag writes the current configuration to `px.ini`:

- Px saves to `--config=path/px.ini` or `PX_CONFIG` if specified.
- Otherwise, it loads `px.ini` from the working, user config or Px directory if
  it exists and updates it.
- If the file is not writable, it tries the next location in order.
- If no `px.ini` is found, it defaults to the user config directory.

### Windows auto-start

Px can be setup to run on startup on Windows:

    pxw --install

Remove with `--uninstall`. Use `--force` to overwrite an existing entry. If
`px.ini` is at a non-default location:

    pxw --install --config=path/px.ini

## Credentials

If SSPI is not available or not preferred, providing `--username` in
`domain\username` format allows Px to authenticate as that user. The password is
retrieved from the system keyring.

```bash
# Set credentials interactively
px --username=domain\username --password

# If username is already configured
px --password
```

Information on keyring backends: <https://pypi.org/project/keyring>

As an alternative, `PX_PASSWORD` or a dotenv file can supply credentials (only
recommended when keyring is not available).

### Windows

Credential Manager is the recommended backend. The password is stored as a
'Generic Credential' with 'Px' as the network address name.

    Control Panel > User Accounts > Credential Manager > Windows Credentials

Or: `rundll32.exe keymgr.dll, KRShowKeyMgr`

### Mac

Keychain Access is used:

- Pick 'Passwords' → 'login' and add a keychain item for 'Px' / 'PxClient'.
- Select 'Always Allow' when prompted.

CLI: `security add-generic-password -s Px -a username -w password`

### Linux

Gnome Keyring or KWallet is used. For headless systems:

```bash
dbus-run-session -- sh
# or in a script:
export DBUS_SESSION_BUS_ADDRESS=$(dbus-daemon --fork --config-file=/usr/share/dbus-1/session.conf --print-address)
echo 'somecredstorepass' | gnome-keyring-daemon --unlock
```

If the default SecretService backend does not work, install a third-party
[backend](https://github.com/jaraco/keyring#third-party-backends).

For Nuitka binaries where keyring is unavailable, use `PX_PASSWORD` instead.

## Client authentication

Px can authenticate downstream clients connecting to it. This is useful in
`gateway` mode where remote clients should log in before using the proxy.

Supported mechanisms: `NEGOTIATE`, `NTLM`, `DIGEST`, `BASIC`.

Client authentication is off by default. Enable with `--client-auth` (recommended
value: `ANYSAFE`).

```bash
px --client-auth=ANYSAFE --client-username=DOMAIN\user
px --client-username=domain\username --client-password
```

SSPI is enabled by default on Windows; disable with `--client-nosspi`.

Multiple client users are supported when using keyring — add each user under the
`PxClient` network address.

## Examples

Use `proxyserver.com:80` and allow requests from localhost only:

    px --proxy=proxyserver.com:80

Don't use any forward proxy at all, just log what's going on:

    px --noproxy=0.0.0.0/0 --debug

Allow requests from `localhost` and all locally assigned IP addresses (useful for
Docker for Windows and VMs in a NAT configuration):

    px --proxy=proxyserver.com:80 --hostonly

Allow requests from localhost, local IPs and specific external IPs:

    px --proxy=proxyserver:80 --hostonly --gateway --allow=172.*.*.*

Allow requests from everywhere (every client will use your login):

    px --proxy=proxyserver.com:80 --gateway

### Docker for Windows

Set your proxy in containers to `http://host.docker.internal:3128` or
`http://<your_ip>:3128`.

    docker build --build-arg http_proxy=http://<your_ip>:3128 --build-arg https_proxy=http://<your_ip>:3128 -t name .

### WSL2

Set up your proxy in `/etc/profile`:

```bash
export http_proxy="http://$(tail -1 /etc/resolv.conf | cut -d' ' -f2):3128"
export https_proxy="http://$(tail -1 /etc/resolv.conf | cut -d' ' -f2):3128"
```

### MQTT over websockets

Increase the idle timeout to 120 seconds (`--idle=120`) since the default MQTT
keepalive period is 60 seconds.

## Dependencies

Px depends on the following Python packages:

- [keyring](https://pypi.org/project/keyring/)
- [netaddr](https://pypi.org/project/netaddr/)
- [pymcurl](https://pypi.org/project/pymcurl/)
- [psutil](https://pypi.org/project/psutil/)
- [pyspnego](https://pypi.org/project/pyspnego/)
- [python-dotenv](https://pypi.org/project/python-dotenv/)
- [quickjs-ng](https://pypi.org/project/quickjs-ng/)

## Limitations

- Mac socket sharing is not implemented at this time and is limited to running
  in a single process.
- The `--hostonly` and `--quit` features do not work on Linux aarch64 systems.
