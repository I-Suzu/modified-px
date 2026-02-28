# Px — Documentation

This folder contains the documentation for the `px-proxy` Python package — an
HTTP proxy server that automatically authenticates through NTLM or Kerberos
proxy servers using Windows SSPI or configured credentials.

## User documentation

| File | Description |
|------|-------------|
| [installation.md](installation.md) | Install via pip, wheels, binary, Docker, scoop; uninstallation |
| [usage.md](usage.md) | Configuration, credentials, client auth, examples, dependencies, limitations |

## Developer documentation

| File | Description |
|------|-------------|
| [architecture.md](architecture.md) | Runtime model, package layout, authentication, proxy discovery |
| [build.md](build.md) | Build system: `pyproject.toml`, `build.sh`, wheels, Nuitka, Docker |
| [configuration.md](configuration.md) | Configuration internals: sources, `State` singleton, option reference |
| [testing.md](testing.md) | Test suite layout, running tests, fixtures, coverage |
| [changelog.md](changelog.md) | Release history |

## Quick reference

- **PyPI name**: `px-proxy`
- **Requires**: Python ≥ 3.10
- **Key dependencies**: [pymcurl](https://pypi.org/project/pymcurl/), [keyring](https://pypi.org/project/keyring/), [netaddr](https://pypi.org/project/netaddr/), [psutil](https://pypi.org/project/psutil/), [pyspnego](https://pypi.org/project/pyspnego/), [quickjs-ng](https://pypi.org/project/quickjs-ng/), [python-dotenv](https://pypi.org/project/python-dotenv/)
