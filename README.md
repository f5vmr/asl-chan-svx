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
## Download

do not clone the repository:
instead perform the following instructions.
```sh
cd /tmp/
wget https://github.com/f5vmr/your choice of OS
dpkg -i the deb of your choice by name.

## Installation


Required dependencies (`asl3-asterisk`, `libopus0`, and `libssl3`) will be installed automatically.

## Configuration

### 1. Create svx.conf

Create `/etc/asterisk/svx.conf` by cp /etc/asterisk/svx.conf.sample 

Configure:
- [Section] number which is the number of the private node.
- Reflector hostname - as you would for a node
- Port as per your selection.
- Login - use the callsign as below as registered in the reflector
- Callsign as registered in the reflector.
- Password as registered in the refelctor
- Talk group
- Monitor 
Optional settings:
Which can be ignored as the standard setting are quite adequate.
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
rxchannel = SVX/<section> where <section> is the private node number
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

Reflectors that provide v2 compatibility can be used even if their SvxLink nodes normally operate using v3/TLS. As our AllStarLink nodes are running V2 and the new SvxReflector is running V3 Now, these none standard callsigns have to be entered like this :
ACCEPT_CALLSIGN="(?:[A-Z0-9][A-Z]{0,2}\\d[A-Z0-9]{0,3}[A-Z](?:-[A-Z0-9]{1,3})?|REFLECTOR-F[A-Z0-9]{2,4}|ASL-[A-Z0-9]+(?:-[A-Z0-9]+)*|NWAG|FREEDOM|BLINDHAMS)" using the PIPE character. They will then be picked up from the main list in [CALLSIGNS] & [PASSWORDS].


## Notes

- Reflector logins must use an **upper-case callsign**. So EarsRadio will have to be changed.
- SVXReflector validates callsigns case-sensitively.
- The package version should match the installed AllStarLink 3 release.
