# Installation

---

## Quick install via pip

If direct internet access is available along with Python (≥ 3.10):

    python -m pip install px-proxy

On Windows, [scoop](https://scoop.sh) can also be used:

    scoop install px

## Offline install via wheels

The whole point of Px is to help tools get through a typical corporate proxy.
This means using a package manager to install Px might not always be feasible
which is why Px offers prebuilt wheels:

1. Download the `wheels` package for the target OS from the
   [releases](https://github.com/genotrance/px/releases) page.
2. Extract and install:

       python -m pip install px-proxy --no-index -f /path/to/wheels

## Standalone binary

If Python is not available, download the latest compiled binary from the
[releases](https://github.com/genotrance/px/releases) page. The Windows binary
is built using Python Embedded and the Mac and Linux binaries are compiled with
[Nuitka](https://nuitka.net) and contain everything needed to run standalone.

## Running Px

Once installed, Px can be run as follows:

- `px` or `python -m px`
- In the background on Windows: `pxw` or `pythonw -m px`
- As a wrapped service using [WinSW](https://github.com/winsw/winsw)

### Running as a Windows service using WinSW

Download the executable `WinSW-x64.exe` from
[WinSW](https://github.com/winsw/winsw) (tested with 2.12.0).

Place a minimal `WinSW-x64.xml` configuration file next to the executable:

```xml
<service>
  <id>Px</id>
  <name>Px Service (powered by WinSW)</name>
  <description>Px HTTP proxy service</description>
  <executable>D:\px\px.exe</executable>
</service>
```

Run from an elevated administrator prompt:

    WinSW-x64.exe install WinSW-x64.xml

Start the service:

    WinSW-x64.exe start WinSW-x64.xml

## Source install

The latest version can be installed from source via pip:

    python -m pip install https://github.com/genotrance/px/archive/master.zip

Or clone and install:

```bash
git clone https://github.com/genotrance/px
cd px
python -m pip install .
```

> **Note:** Source install methods require internet access since Python will try
> to install Px dependencies from the internet. The wheels mentioned above can
> bootstrap a source install.

### Without installation

Px can be run as a local Python script without installation:

```bash
pip install keyring netaddr psutil pymcurl pyspnego python-dotenv quickjs-ng

pythonw px.py  # run in the background
python px.py   # run in a console window
```

## Uninstallation

If Px has been installed to the Windows registry to start on boot, uninstall
first:

    px --uninstall

Then remove via pip:

    python -m pip uninstall px-proxy

## Docker

Px is available as a prebuilt Docker
[image](https://hub.docker.com/r/genotrance/px).

Two images are posted — the default includes keyring and associated dependencies
whereas the mini version is smaller but requires `PX_PASSWORD` and
`PX_CLIENT_PASSWORD` environment variables for credentials.

### Docker flags

```
--name px       name container so it is easy to stop it
-d              run in the background
--rm            remove container on exit
```

### Networking

```
--network host  make Px directly accessible from host network
  OR
-p 3128:3128    publish the port — Px needs to run in --gateway mode
```

### Configuration

```
-e PX_LOG=4     set environment variables to configure Px

-v /dir:/px     mount a host directory with a px.ini or .env file
  OR
--mount source=/dir,target=/px

docker run ... genotrance/px --gateway --verbose
                configure directly from the command line
```

### Credentials

Keyring credentials can be stored in a host folder and mounted:

```
-v /keyrings:/root/.local/share/keyrings
  OR
--mount source=/keyrings,target=/root/.local/share/keyrings
```

Save credentials via CLI:

```
docker run ... genotrance/px --username=... --password
```

The mini image requires environment variables:

```
-e PX_PASSWORD=... -e PX_CLIENT_PASSWORD=...
```
