# Jochona Constellation

Planning docs only — no code lives in this repository yet. Constellation
is the planned owner-hosted management plane described here; it is not
built or running anywhere.

- [`CONTEXT.md`](CONTEXT.md) — vocabulary and domain language.
- [`docs/architecture.md`](docs/architecture.md) — system architecture.
- [`docs/implementation-plan.md`](docs/implementation-plan.md) — build sequencing.
- [`docs/plan-1.0.md`](docs/plan-1.0.md) — the 1.0 scope and status of the shipping repos below.
- [`docs/adr/`](docs/adr/) — architecture decision records.

## The Jochona repositories

Constellation is one of five repositories; the other four ship real code today:

- [Jochona Host](https://github.com/Jochona/jochona-host) — the GameStream streaming server (Sunshine fork), runs on the machine you game on.
- [Jochona Client](https://github.com/Jochona/jochona-client) — the controller-first streaming client (Moonlight Qt fork), runs on the device you play from.
- [Jochona Display Adapter](https://github.com/Jochona/jochona-display-adapter) — optional Windows virtual-display driver Host leases for headless/virtual-display sessions.
- [Jochona Beacon](https://github.com/Jochona/jochona-beacon) — optional Linux LAN daemon for Wake-on-LAN and presence when a direct broadcast domain can't reach the Host.

Constellation itself is planning-only: it will eventually add remote wake, trusted-friend access, and owner-run
Proxmox VM fleet management on top of the four repos above, without ever becoming a required stream hop. See
[`docs/plan-1.0.md`](docs/plan-1.0.md) for exactly what each repo ships in 1.0 and what's still planned.

## Getting started: Windows Host + Bazzite Client

The most common 1.0 setup: Jochona Host on a Windows gaming PC, Jochona Client on a Steam Deck/handheld running
Bazzite (or any Linux desktop), streaming over your own LAN. No Constellation, no account, nothing in the middle.

1. **Install Jochona Host on Windows.** Download the latest release (or a CI build, if no tag exists yet) from
   [Jochona Host's releases](https://github.com/Jochona/jochona-host/releases/latest) — see
   [Getting Started > Jochona Host Install](https://github.com/Jochona/jochona-host/blob/main/docs/getting_started.md#jochona-host-install)
   for the exact asset per architecture. Run the installer (or unzip the portable build) and launch Sunshine; it
   opens its web UI at `https://localhost:47990`.
2. **(Optional) Install the DualSense (DS5) driver.** If you want full DualSense features (touchpad, motion,
   adaptive triggers) instead of the default Xbox/DS4-class emulation, follow
   [Getting Started > DualSense on Windows](https://github.com/Jochona/jochona-host/blob/main/docs/getting_started.md#dualsense-on-windows)
   to install the [libvirtualgamepad](https://github.com/Nonary/libvirtualgamepad) VHF driver, or see
   [Jochona Host's gamepad docs](https://github.com/Jochona/jochona-host/blob/main/docs/gamepads.md) for the full
   backend matrix. Skip this step to keep the default ViGEmBus backend.
3. **Install Jochona Client on Bazzite.** Bazzite is an immutable Fedora variant, so the AppImage is the right
   fit — no `rpm-ostree install`, no layering, no reboot. See
   [Jochona Client's install docs](https://github.com/Jochona/jochona-client/blob/main/docs/install.md#bazzite)
   for the exact download (release asset once tagged, or a CI artifact today) and how to add it as a Game Mode
   shortcut.
4. **Pair.** Open Jochona Client; if the Host is on the same LAN it should appear automatically via mDNS. Select
   it — the Client shows a pairing PIN. On the Host, open `https://localhost:47990`'s pairing page and enter that
   PIN within about 60 seconds. Once paired, the Host shows as trusted and you can launch a stream.
5. **(Optional) Wake-on-LAN.** The Client can wake a sleeping/powered-off Host automatically using its captured
   MAC address, but only directly on the same LAN broadcast domain (no VPN/overlay or routed-subnet wake yet). If
   the Client and Host don't share a broadcast domain, add [Jochona Beacon](https://github.com/Jochona/jochona-beacon)
   as an always-on Linux helper on the Host's LAN — it pairs with the Client the same way and takes over wake duty
   without ever being able to launch, stop, or otherwise control the Host.

That's the whole 1.0 path: no Constellation account, no cloud relay, nothing you don't run yourself.

## License

[`LICENSE`](LICENSE) — planning documents only; no code is published from this repository yet.
