# Build & Distribution

---

## Overview

Px is a pure Python application but depends on several packages that have OS and
machine specific binaries. As a result, Px ships two kinds of binaries:

- **Wheels** — all packages needed to install Px on supported versions of Python.
- **Binary** — compiled binary using Python Embedded on Windows and Nuitka on Mac
  and Linux.

These binaries are provided for the following platforms:
- Windows — amd64
- Mac — x86_64, arm64
- Linux — x86_64, aarch64 for glibc and musl based systems

## `pyproject.toml`

Package metadata, dependencies, and all tool configuration (ruff, mypy, pytest,
coverage, tox) live in `pyproject.toml`. The build backend is
`setuptools.build_meta`.

## `build.sh` and `build.ps1`

The repository provides build scripts for Linux/Mac (`build.sh`) and Windows
(`build.ps1`).

### Building wheels

```bash
# Build dependency wheels (glibc)
./build.sh -b -d -i glibc

# Build dependency wheels (musl/Alpine)
./build.sh -b -d -i musl
```

Wheels are stored under `wheel/`.

### Building binaries

```bash
# Build Nuitka binary (Linux/Mac)
./build.sh -b -n

# Build embedded binary (Windows)
.\build.ps1 -b
```

### Full release builds

Refer to `build.sh` for the complete list of supported images and options
(e.g. `-v` for Python version, `-a` for architecture).

## `tools.py`

Additional build targets:
- `tools.py --docker` — builds `genotrance/px` Docker images (full and mini).
- `tools.py --embed` — downloads a Python embeddable distribution, installs
  the wheel, and packages `px.exe`.

## Docker

Px is available as a prebuilt Docker image at `genotrance/px`. Two variants
are posted — the default includes keyring and dependencies, while the mini
version is smaller but requires `PX_PASSWORD` and `PX_CLIENT_PASSWORD`
environment variables for credentials.
