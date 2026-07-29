# asl3-chan-svx

**chan_svx** is an Asterisk channel driver for **AllStarLink 3** that connects directly to a **SvxLink SVXReflector**.

Unlike traditional solutions that rely on a USRP bridge, `chan_svx` speaks the SVXReflector protocol natively, providing:

- Lower latency
- Simpler system architecture
- A single Opus transcoding step
- Reduced CPU and memory usage
- Direct integration with `app_rpt`

This allows SVXReflector talk groups to be used directly from AllStarLink without an intermediate USRP connection.

## Features

- Native SVXReflector connectivity
- Direct integration with AllStarLink 3 (`app_rpt`)
- SVXReflector v2 protocol support (HMAC-SHA1)
- Configurable jitter buffer
- Adjustable RX/TX gain
- Optional RX/TX AGC
- Support for x86-64 and ARM64 (Raspberry Pi)

Source code is private.

Bug reports and support requests: **bjorne@sa7aux.se**

## Requirements

- Debian 12 (Bookworm) or Debian 13 (Trixie)
- AllStarLink 3 (`asl3-asterisk`) already installed
- Access credentials for an SVXReflector

## Download

Clone the repository:

```sh
git clone https://github.com/<your-repository>/asl3-chan-svx.git
cd asl3-chan-svx
```

## Choose the Correct Package

Verify your Debian release:

```sh
. /etc/os-release
echo $VERSION_CODENAME
```

Verify your CPU architecture:

```sh
dpkg --print-architecture
```

| Platform | Package |
|-----------|----------|
| Debian 13 (Trixie), x86-64 | `debs/asl3-chan-svx_0.2.5~trixie_amd64.deb` |
| Debian 13 (Trixie), ARM64 (Raspberry Pi OS Trixie) | `debs/asl3-chan-svx_0.2.5~trixie_arm64.deb` |
| Debian 12 (Bookworm), x86-64 | `debs/asl3-chan-svx_0.2.5~bookworm_amd64.deb` |
| Debian 12 (Bookworm), ARM64 (Raspberry Pi OS Bookworm) | `debs/asl3-chan-svx_0.2.5~bookworm_arm64.deb` |

Only 64-bit platforms are supported.

## Installation

Install the package that matches your operating system and architecture:

```sh
sudo apt install ./debs/asl3-chan-svx_0.2.5~<os>_<arch>.deb
```

Required dependencies (`asl3-asterisk`, `libopus0`, and `libssl3`) will be installed automatically.

## Configuration

### 1. Create svx.conf

Create `/etc/asterisk/svx.conf` and configure:

- Reflector hostname
- Port
- Callsign
- Password
- Talk group

Optional settings:

- `tx_hold`
- `jitter`
- `rx_gain`
- `tx_gain`
- `rx_agc`
- `tx_agc`

### 2. Enable the Channel Driver

Edit `/etc/asterisk/modules.conf` and enable the module:

```ini
load = chan_svx.so
```

If a line already exists as:

```ini
;load = chan_svx.so
```

remove the leading semicolon.

### 3. Configure app_rpt

Edit `/etc/asterisk/rpt.conf` and set:

```ini
rxchannel = SVX/<section>
```

where `<section>` matches the section name in `svx.conf`.

### 4. Restart Asterisk

```sh
sudo asterisk -rx "core restart now"
```

### 5. Verify Operation

Check the driver status:

```sh
sudo asterisk -rx "svx show"
```

A successful connection will display reflector and talk group information.

## Documentation

A complete installation and configuration guide is included with the package:

```text
/usr/share/doc/asl3-chan-svx/INSTALL.md
```

The package post-install script (`postinst`) also displays the required setup steps after installation.

## Compatibility

`chan_svx` implements the SVXReflector v2 protocol (HMAC-SHA1).

Reflectors that provide v2 compatibility can be used even if their SvxLink nodes normally operate using v3/TLS.

## Notes

- Reflector logins must use an **upper-case callsign**.
- SVXReflector validates callsigns case-sensitively.
- The package version should match the installed AllStarLink 3 release.
