# Jochona Constellation Implementation Plan

Status: accepted plan

The plan ships complete vertical slices. Each slice ends in observable behavior and removes all temporary scaffolding before release.

## Delivery order

1. Owner Proxmox lifecycle.
2. Beacon wake and presence.
3. Friends, delegated access, and approval.
4. Constellation Relay.

The first public build is experimental 0.x. Stable claims require the release gates in this plan.

## Cross-cutting rules

- Keep one Rust workspace with `constellationd` and `jochona-relay` binaries.
- Keep the TypeScript administration UI in the same repository.
- Treat checked-in OpenAPI, JSON Schema, CDDL, and vectors as protocol authority.
- Put policy and lifecycle behavior behind the `constellation-core` interface.
- Keep transport and Provider details in adapters at explicit seams.
- Use migrations for every durable schema change.
- Add tests for observable contracts, failure transitions, and security invariants.
- Do not publish a page or route that uses simulated data.
- Do not add a second Provider until the Proxmox adapter and Provider interface prove the seam.

## Slice 1: Owner Proxmox lifecycle

### Phase 0: Repository and protocol contracts

Deliverables:

- Create the AGPL-3.0 Rust workspace, TypeScript web project, license notices, and locked dependencies.
- Define stable identifiers for Organization, Fleet, Machine, Host, Host Application, Principal, Device, Connector, Operation, Reservation, Lease, and Hold.
- Check in initial OpenAPI, JSON Schema, CDDL, and cross-language golden vectors.
- Implement envelope version negotiation and required-feature rejection.
- Add SQLite migration preflight, one-way apply, and newer-schema refusal.
- Add deterministic clocks, random sources, and in-memory adapters for core tests.

Exit gate:

- Rust and TypeScript round-trip every shared vector with identical canonical bytes.
- Unknown optional fields survive or are ignored as specified.
- Unknown required features fail before state mutation.
- A repeated migration run is idempotent and creates no new backup.

### Phase 1: Bootstrap, identity, and permission core

Deliverables:

- Implement the one-use local bootstrap secret and canonical browser-origin checks.
- Implement passkey-first Member authentication and Device enrollment.
- Implement Protected Root Owner authority and separate Owner Device keys.
- Implement scoped allow-only Permission union and no-amplification checks.
- Implement field-level permission and redaction rules.
- Implement canonical CBOR and COSE verification for authority objects.
- Append identity and policy changes to the tamper-evident audit log.

Web surface:

- Bootstrap and sign-in.
- Organization identity and Root Owner devices.
- Device list, revocation, and authentication history.
- Audit list with source, severity, and export.

Exit gate:

- The server cannot create an Owner signature.
- A delegated Role cannot grant a Permission outside its assigner's scope.
- Revoking one Device does not revoke sibling Devices.
- Protected Root Owner authority survives every custom Role mutation.

### Phase 2: Persistence and operations foundation

Deliverables:

- Implement SQLite WAL repositories for all Slice 1 records.
- Implement envelope encryption with an external master key.
- Implement encrypted backups, restore preflight, Deployment Epoch fencing, and scrubbed clones.
- Implement separate liveness and authenticated readiness checks.
- Implement graceful process shutdown and mutation draining.
- Implement in-product notifications and signed webhook delivery.
- Implement retention and redaction for audit and local operational history.

Web surface:

- Backup creation, restore preflight, and recovery warning.
- Health and migration state.
- Notification delivery failures.

Exit gate:

- A backup does not reveal Connector secrets without the master key.
- A restored database cannot control Machines until the new epoch receives Root Owner approval.
- Two Deployments with one epoch cannot both pass readiness.
- Shutdown leaves no half-committed Operation.

### Phase 3: Proxmox Provider adapter

Deliverables:

- Implement the narrow Provider Port in `constellation-core`.
- Implement the Proxmox adapter for state reads, start, graceful shutdown, and task outcomes.
- Implement endpoint pinning and privilege-separated API-token storage.
- Implement read-only Connector verification for identity, permissions, nodes, and VM visibility.
- Implement explicit Connector, node, VMID, Machine, and Host binding.
- Implement normalized Provider errors with raw provenance available to authorized operators.
- Implement per-Machine fencing and observe-before-retry reconciliation.

Web surface:

- Connector setup and read-only test.
- Fleet and Machine registration.
- Explicit Proxmox node and VMID binding.
- Provider evidence, native task IDs, and drift explanations.

Exit gate:

- The token cannot create, delete, reconfigure, or migrate a VM.
- A changed Proxmox certificate blocks control until an owner accepts the new pin.
- Operation success requires a successful task and a fresh target-state Observation.
- Concurrent start and stop requests resolve through one Machine fence.

### Phase 4: Host channel and Client enrollment

Deliverables:

- Implement Jochona Host enrollment with owner approval and pinned mutual TLS.
- Implement the outbound Host channel with versioned CBOR envelopes and resumable sequence numbers.
- Ingest Host readiness, capacity, Host Application catalog, and Session snapshots.
- Implement atomic short Reservations and the busy-Host response.
- Implement pinned-deployment enrollment for one Jochona Client Organization Profile.
- Give each Client Profile a separate Device key, cache, settings store, and credential set.
- Block Profile switching while a Session or unresolved Operation exists.
- Implement direct-path route material without making Constellation a stream hop.

Exit gate:

- Replayed or out-of-order Host snapshots cannot roll state backward.
- A Host identity change blocks management and launch.
- Two launch requests cannot acquire one single-Session Host capacity slot.
- Removing a Client Profile revokes its Device before local secrets are erased.
- A valid direct Session can continue during a Constellation outage.

### Phase 5: Lifecycle coordinator and real Client journey

Deliverables:

- Implement the deep Control Plane interface for commands, Observations, and Projections.
- Implement intent Leases, active Leases, Holds, expiry, cancellation, and failed-boot cleanup.
- Implement separate desired, infrastructure, Host, Session, and route axes.
- Implement evidence freshness and user-facing Projection sentences.
- Implement the ten-minute default idle grace and graceful Proxmox shutdown.
- Implement explicit conflict responses for start-during-stop and busy Host cases.
- Implement reconnect without creating a duplicate active Lease.
- Connect Jochona Client launch and Session evidence to Constellation.

Exit gate scenario:

1. An Owner selects a Host Application on a stopped registered Proxmox QEMU Machine.
2. Constellation authorizes the request and obtains a Reservation and intent Lease.
3. Proxmox starts the Machine and reports a successful task and running state.
4. Jochona Host connects, reports readiness, and confirms the reservation.
5. Jochona Client starts a direct GameStream Session.
6. The Host attests the Session, and the Lease becomes active.
7. The Client reconnects once without losing authority or duplicating occupancy.
8. The Owner ends the Session.
9. Constellation observes no remaining Lease or Hold and waits ten minutes.
10. Proxmox performs a graceful shutdown and reports the stopped state.
11. The audit history explains every authority, lifecycle, and evidence transition.

### Phase 6: Operational web surface and experimental release

Deliverables:

- Build the Fleet operations overview as the dashboard home.
- Show every Machine with an evidence-based status sentence and freshness.
- Show Operations, Reservations, Leases, Holds, and recovery actions.
- Complete Connector, Host, Client Device, audit, backup, health, and notification pages.
- Add explicit confirmations for destructive root, restore, and emergency-stop actions.
- Publish signed Linux binary and OCI artifacts with provenance and SBOM.
- Publish supported Proxmox, Windows guest, Host, Client, and Linux deployment versions.

Release gate:

- The full Phase 5 scenario passes on the protected hardware lane.
- Backup restore and epoch fencing pass in a clean environment.
- The web UI contains no future page with fake or disabled data.
- Documentation labels the release experimental 0.x and names the exact supported path.
- Documentation warns that stopped VMs can retain storage, backup, network, license, and hardware costs.

Slice 1 non-goals:

- Member invitations, Guest Grants, custom-Role editing, and launch approvals.
- Beacon enrollment, remote wake, Relay allocation, and Relay transport.
- VM creation, deletion, configuration, migration, snapshots, storage, networking, or billing.
- AWS, Azure, GCP, Kubernetes, libvirt, and arbitrary command adapters.
- High availability, multi-Organization tenancy, mobile administration, and unattended hard shutdown.

## Slice 2: Beacon wake and presence

Deliverables:

- Implement Beacon enrollment as one Organization-scoped outbound identity.
- Implement independent LAN presence Observations and explicit freshness.
- Implement signed, short-lived Wake Tickets for one Machine and intent.
- Preserve the direct Jochona Client-to-Beacon wake route during Constellation outages.
- Add Beacon health, route, wake receipt, and failed-wake Projections.
- Add a Machine Power Strategy that selects Beacon without guessing from reachability.

Exit gate:

- A stopped home Machine can wake through an enrolled Beacon from an Organization Client Profile.
- A Constellation outage does not remove an already paired direct wake route.
- A Wake Ticket cannot wake another Machine, survive expiry, or authorize launch.
- Beacon presence never proves that Jochona Host is Session-free.

Slice 2 non-goals:

- Stream Deck control, public Relay transport, Guest access, and automatic Provider fallback.

## Slice 3: Friends and delegated access

Deliverables:

- Implement signed one-use Member and Device invitations.
- Implement custom Role creation with no-amplification enforcement.
- Implement Owner-signed Grants with Host Application, time, concurrency, and bandwidth scope.
- Implement optional per-launch approval and visible pending state.
- Implement native Owner Device signing on Windows, macOS, and Linux.
- Implement configurable hardware-key posture and optional attestation policy input.
- Implement proposal expiry, quorum collection, and independent Root Owner recovery keys.
- Implement granted-resource-only discovery and the 24-hour default offline validity bound.

Exit gate:

- A Guest sees only resources named by an active Grant.
- Constellation cannot convert an unsigned request into a valid Grant.
- Revocation blocks future online launch and never extends offline validity.
- A lost Device can be revoked without rotating every Member Device.
- Root changes require the configured number of independent, fresh signatures.

Slice 3 non-goals:

- Public registration, social discovery, password recovery by Jochona, and server-held Owner signing keys.

## Slice 4: Constellation Relay

Deliverables:

- Build `jochona-relay` as a separately deployable binary.
- Implement Relay node enrollment, health, capacity, quotas, and signed accounting.
- Implement preflight and renewable capacity Holds.
- Implement attenuated path tickets bound to Grant, Client, Host, Host Application, node, quotas, and expiry.
- Implement authenticated multiplexed QUIC streams and datagrams.
- Implement degraded TLS transport on port 443 when QUIC fails.
- Require Jochona native full GameStream encryption and Host enforcement.
- Implement automatic direct-first path selection and visible reconnect after allocation loss.
- Disclose operator-visible metadata before Relay use.
- Implement signed webhooks and critical alerts for trust or control anomalies.

Exit gate:

- No Relay packet forwards before both endpoints prove allocation tickets.
- A ticket cannot become a general proxy or cross its bound Organization, Host, Client, or Host Application.
- Address migration succeeds only for authenticated endpoints in the same allocation.
- Relay restart loses allocations and produces a controlled reconnect.
- Capacity and bandwidth exhaustion fail before the Machine launch acquires a long Lease.
- Full native stream encryption remains active on both QUIC and TLS fallback paths.

Slice 4 non-goals:

- A public Jochona relay pool, content decryption, Host authorization, Machine control, or durable Relay allocation state.

## Stable-release gate

A 1.0 claim requires all applicable conditions:

- Shared consumer tests and golden vectors pass across every shipped repository.
- Every untrusted parser has sustained fuzz coverage with stored regressions.
- Real Proxmox, Windows Host, Client, Beacon, and Relay lanes pass their supported scenarios.
- Upgrade, rollback refusal, backup restore, and split-brain fencing pass.
- Release artifacts have signatures, reproducible provenance, an SBOM, and dependency locks.
- The threat model is public and current.
- An independent review covers Friends authority and Relay abuse resistance.
- Product copy distinguishes shipped, experimental, and planned behavior.

## First implementation checkpoint

Implementation starts with Phase 0 only. It ends when Rust and TypeScript share canonical vectors and migration safety works.

Phase 1 starts only after those contracts stop changing daily. This sequence prevents transport handlers and UI code from inventing incompatible domain shapes.
