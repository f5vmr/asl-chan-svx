# asl3-chan-svx — prebuilt packages

Prebuilt `.deb` packages for **chan_svx**, an Asterisk channel driver for
AllStarLink 3 (app_rpt) that speaks the SvxLink **SVXReflector** protocol
directly — no USRP hop, single Opus transcode.

Source code: private. Bug reports: **bjorne@sa7aux.se**.

## Which package?

Pick by your OS (Debian codename) and CPU architecture. Check with
`. /etc/os-release; echo $VERSION_CODENAME` and `dpkg --print-architecture`.

| Platform | Package |
|---|---|
| Debian 13 **trixie**, x86-64 | `debs/asl3-chan-svx_0.2.3~trixie_amd64.deb` |
| Debian 13 **trixie**, arm64 (Raspberry Pi OS trixie, Pi 3/4/5) | `debs/asl3-chan-svx_0.2.3~trixie_arm64.deb` |
| Debian 12 **bookworm**, x86-64 | `debs/asl3-chan-svx_0.2.3~bookworm_amd64.deb` |
| Debian 12 **bookworm**, arm64 (Raspberry Pi OS bookworm) | `debs/asl3-chan-svx_0.2.3~bookworm_arm64.deb` |

64-bit only. Requires AllStarLink 3 (`asl3-asterisk`) already installed —
the package must match that exact release, so use the deb for your OS.

## Install

```sh
sudo apt install ./debs/asl3-chan-svx_0.2.3~<os>_<arch>.deb
```

Dependencies (`asl3-asterisk`, `libopus0`, `libssl3`) resolve automatically.

Then, to enable it:

1. Create `/etc/asterisk/svx.conf` (host, port, login, password, callsign, tg —
   plus optional `tx_hold`, `jitter`, `rx_gain`/`tx_gain`, `rx_agc`/`tx_agc`).
2. Uncomment `load = chan_svx.so` in `/etc/asterisk/modules.conf`.
3. Set `rxchannel = SVX/<section>` on your node in `/etc/asterisk/rpt.conf`.
4. `sudo asterisk -rx "core restart now"` then `sudo asterisk -rx "svx show"`.

`postinst` prints these steps, and a full walkthrough ships in
`/usr/share/doc/asl3-chan-svx/INSTALL.md`.

## Notes

- Reflector logins are used **upper-case** (SvxReflector validates the callsign).
- Speaks SVXReflector **v2** (HMAC-SHA1). Reflectors that also offer v2 work even
  if their nodes normally use v3/TLS.
