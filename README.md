[![Chat on Gitter](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/genotrance/px)
[![Chat on Matrix](https://img.shields.io/matrix/genotrance_px:matrix.org)](https://matrix.to/#/#genotrance_px:matrix.org)

# Px

An HTTP(s) proxy server that allows applications to authenticate through an
NTLM or Kerberos proxy server, typically used in corporate deployments, without
having to deal with the actual handshake. Px leverages Windows SSPI or single
sign-on and automatically authenticates using the currently logged in Windows
user account. It is also possible to run Px on Windows, Linux and MacOS without
single sign-on by configuring the domain, username and password to authenticate
with.

Px uses libcurl and supports all the authentication mechanisms supported by
[libcurl](https://curl.se/libcurl/c/CURLOPT_HTTPAUTH.html).

**Requires Python ≥ 3.10**

## Quick start

```bash
# Install
python -m pip install px-proxy

# Run with an upstream proxy
px --proxy=proxyserver.com:8080

# Run with a PAC file
px --pac=http://example.com/proxy.pac

# Run with verbose logging
px --proxy=proxyserver.com:80 --verbose
```

See `px --help` for all options.

## Documentation

| | |
|---|---|
| **User guides** | |
| [Installation](docs/installation.md) | pip, wheels, binary, Docker, scoop, WinSW, uninstallation |
| [Usage](docs/usage.md) | Configuration, credentials, client auth, examples, dependencies, limitations |
| **Developer guides** | |
| [Architecture](docs/architecture.md) | Runtime model, package layout, authentication, proxy discovery |
| [Build](docs/build.md) | Build system, `pyproject.toml`, `build.sh`, wheels, Nuitka, Docker images |
| [Configuration internals](docs/configuration.md) | `State` singleton, option reference, config sources |
| [Testing](docs/testing.md) | Test suite layout, running tests, fixtures, coverage |
| **Reference** | |
| [Changelog](docs/changelog.md) | Release history |

## Development

Requires [uv](https://docs.astral.sh/uv/).

```bash
git clone https://github.com/genotrance/px.git
cd px
make install
make test
```

| Target | Description |
|---|---|
| `make install` | Create venv, install dependencies, install pre-commit hooks |
| `make check` | Run linters (ruff) and type checking (mypy) |
| `make test` | Run tests with coverage |
| `make build` | Build sdist and wheel |
| `make clean` | Remove build artifacts |

## Contributing

Bug reports and pull requests are welcome at
<https://github.com/genotrance/px/issues>.

1. Fork and clone the repository.
2. `make install` to set up the venv and pre-commit hooks.
3. Create a feature branch, make changes, add tests in `tests/`.
4. `make check && make test` — all checks must pass.
5. Open a pull request.

## Feedback

Px is definitely a work in progress and any feedback or suggestions are welcome.
It is hosted on [GitHub](https://github.com/genotrance/px) with an MIT license
so issues, forks and PRs are most appreciated. Join us on the
[discussion](https://github.com/genotrance/px/discussions) board,
[Gitter](https://gitter.im/genotrance/px) or
[Matrix](https://matrix.to/#/#genotrance_px:matrix.org) to chat about Px.

## Credits

Thank you to all
[contributors](https://github.com/genotrance/px/graphs/contributors) for their
PRs and all issue submitters.

Px is based on code from all over the internet and acknowledges innumerable
sources.
