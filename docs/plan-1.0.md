# Jochona 1.0 plan

Scope of **1.0.0**: what already works end to end (Host + Client + display-adapter + optional Beacon) with a
trustworthy CI/CD path, accurate docs, and downloadable releases. Everything in the research docs that is not listed
under "1.0 scope" is **post-1.0**.

Research inputs (corrected baseline): `research/capture-encode-bleeding-edge.md`,
`research/virtual-display-landscape.md`, and `host/docs/research/vibepollo-feature-comparison.md`.

## Licensing rules

- **Host** and **Client** are GPLv3: GPLv3-compatible code (Sunshine, Apollo, Vibepollo, Solarflare, WiVRn, ...) may be
  ported with attribution, preserved headers, and a per-file/per-dependency license check.
- **display-adapter** and any MIT/Apache repo: never paste GPL code. Re-implement from specs and MIT/MS-PL sources.

## 1.0 scope

| Item | Repo | Status |
|---|---|---|
| Windows Host installer builds in CI (AMD64 + ARM64) | host | done |
| Host fork CI gated (no LizardByte-only secrets/targets), manual runs work | host | done |
| Strict Doxygen clean | host | done |
| Linux AppImage / Windows / macOS Client builds | client | done |
| display-adapter driver builds x64 + ARM64, tests green | display-adapter | done |
| Windows DualSense (DS5) virtual gamepad + adaptive triggers (VHF backend, ViGEm fallback) | host | done |
| Release workflow on `v*` tags, SHA256SUMS, unsigned-binaries notice | client, display-adapter, beacon | done |
| Fork-owned release workflow on `v*` tags, SHA256SUMS, unsigned-binaries notice | host | in progress (merged to main; dry-run + throwaway-tag proof pending) |
| Docs updated per repo, install paths verified against real release assets | all | in progress (host, display-adapter, beacon, constellation done; client docs PR open) |
| Tag + publish `v1.0.0` | host, client, display-adapter, beacon | pending |

Release rules: cut releases only from committed `main` (no local WIP), all CI green, assets downloaded and smoke-checked
against the install docs. Binaries are **unsigned**; display-adapter additionally needs test-signing or the user's own
signing (documented in its README).

## Post-1.0 roadmap (ranked)

1. **Cross-platform Vulkan PyroWave** (port Solarflare/WiVRn GPL code into Host, Aurora decoder ideas into Client).
   Current implementation is Metal/macOS only.
2. **Multi-display slot pool** in display-adapter (protocol v1.1; today `MAX_SLOTS=1`).
3. **HDR metadata authoring + hardware cursor + per-lease EDID** in display-adapter.
4. **macOS capture: ScreenCaptureKit** instead of `AVCaptureScreenInput`.
5. **Blackwell NVENC**: 4:2:2, AV1 UHQ, split-frame encode.
6. **Portal/PipeWire hardening** (already shipping in `portalgrab.cpp`/`pipewire.cpp`): HDR tagging for all 10-bit
   formats, metadata cursor mode, `SOURCE_TYPE_VIRTUAL`, per-interface restore tokens, hybrid-GPU selection.
7. **Display persistence hardening** (already uses libdisplaydevice): Linux backend, forced revert retry for the
   24H2 stuck-device case, defined contract with display-adapter slot teardown.
8. **WGC service-mode capture** (Vibepollo, validate against upstream Sunshine#2846).
9. Vibepollo parity: ViGEm-unavailable fallback UX, DS5 option in Web UI, scoped API tokens, update notifications,
   session auth, RTSS/NVCP frame pacing, Playnite.
10. Windows/macOS Vulkan Video encode (already present for Linux in `video.cpp`).

## Versioning

`1.0.0` for host, client, display-adapter and beacon (tags `v1.0.0`). Host/client previously used upstream calendar
versions; from 1.0 the tag is the version.
