# Jochona Constellation

Jochona Constellation is the owner-controlled management plane for streamable machines. It coordinates authority, power, presence, access, and session intent.

## Language

### Trust and tenancy

**Organization**:
The trust domain that owns identities, root keys, policy, and Fleets. One Constellation Deployment serves exactly one Organization.
_Avoid_: tenant, account, deployment

**Constellation Deployment**:
One running Constellation installation and its state for one Organization. A second Organization requires a separate Deployment.
_Avoid_: instance, cluster, server

**Fleet**:
A non-overlapping operational group of Machines within one Organization. Every managed Machine belongs to exactly one Fleet.
_Avoid_: Organization, pool, group

**Principal**:
A Member, Service Principal, or Device that can request an action.
_Avoid_: user, account, actor

**Member**:
A human identity within an Organization. A Member can own multiple independently enrolled Devices.
_Avoid_: user, account, person record

**Service Principal**:
A non-human identity for approved automation within an Organization.
_Avoid_: bot user, API user, service account

**Device**:
An independently enrolled cryptographic identity owned by a Member or Service Principal. The Organization can revoke one Device without revoking its owner.
_Avoid_: client, installation, shared key

**Owner Device**:
A Device that Organization policy permits to sign authority objects. Each Owner Device has an independent signing key.
_Avoid_: owner account, server signer, admin browser

**Root Owner**:
The protected authority that governs trust anchors and the limits of delegated administration. A custom Role cannot create or modify Root Owner authority.
_Avoid_: super admin, unrestricted role, operator

**Role**:
A named set of scoped Permissions that an Organization binds to Principals.
_Avoid_: grant, group, access level

**Permission**:
An allowed action over a defined resource scope. Constellation combines Permissions by union and has no deny rule.
_Avoid_: role, entitlement, privilege flag

**Grant**:
An Owner-signed delegation that lets a Principal discover and launch specified Host Applications under explicit limits.
_Avoid_: Role, invitation, entitlement

**Invitation**:
A signed, one-use offer to enroll a new Member or Device into an Organization.
_Avoid_: Grant, login link, shared secret

**Quorum Proposal**:
An expiring authority change that requires the configured number of independent Owner Device signatures.
_Avoid_: approval request, vote, server-side change

### Machines and routes

**Machine**:
A physical computer or virtual machine with one lifecycle strategy. A Machine can exist before its Host is reachable.
_Avoid_: Host, server, VM record

**Host**:
The enrolled Jochona Host identity and process on one Machine. A Host reports readiness, capacity, Host Applications, and Sessions.
_Avoid_: Machine, Sunshine host, computer

**Host Application**:
A stable launchable entry that a Host exposes. It can represent a game, launcher, utility, or desktop.
_Avoid_: game, process path, app

**Provider**:
An external system that controls a Machine's infrastructure lifecycle.
_Avoid_: Connector, cloud, hypervisor account

**Connector**:
One configured and authenticated relationship between a Constellation Deployment and a Provider endpoint.
_Avoid_: Provider, credentials, integration

**Power Strategy**:
The configured method that can start or stop one Machine. A strategy can use a Provider, Beacon, or direct wake path.
_Avoid_: wake provider, lifecycle state, fallback guess

**Beacon**:
An enrolled Jochona Beacon identity that supplies independent LAN presence and scoped wake capability.
_Avoid_: relay, probe, Raspberry Pi

**Constellation Relay**:
A separately deployed Constellation role that forwards one authorized Session allocation. It cannot grant access or control a Machine.
_Avoid_: Jochona Relay product, proxy, authorization server

**Client Profile**:
One isolated Jochona Client trust context for one Organization. It has separate credentials, caches, settings, and Device identity.
_Avoid_: account, Organization, streaming profile

**Client Device**:
One physical installation that runs Jochona Client. Each Client Profile enrolls a separate Device identity for that installation.
_Avoid_: Client, Device (when meaning the physical installation), Profile

**Local Profile**:
The permanent Jochona Client context that uses direct host pairing without an Organization.
_Avoid_: offline account, default Organization, guest profile

### Work and evidence

**Reservation**:
A short atomic claim against Host capacity before a launch starts.
_Avoid_: Lease, queue position, Session

**Lease**:
A Principal-scoped reason to keep a Machine active while launch intent or a Session exists.
_Avoid_: Reservation, login session, timeout

**Hold**:
An explicit, expiring reason to keep a Machine active without a Session.
_Avoid_: Lease, maintenance mode, permanent override

**Session**:
One streaming connection between one Client Device and one running Host Application.
_Avoid_: Lease, stream state, connection attempt

**Operation**:
The durable record of a requested control action, its progress, and its outcome.
_Avoid_: task, command, state

**Observation**:
Timestamped evidence from a Provider, Host, Beacon, Client, or Relay.
_Avoid_: status, Projection, cached truth

**Projection**:
A user-facing interpretation of current Operations and Observations. It includes source, freshness, and uncertainty.
_Avoid_: Observation, raw state, truth

**Desired Action**:
The current requested lifecycle intent for a Machine, such as none, start, or stop.
_Avoid_: Machine state, Operation status, target status

**Infrastructure State**:
The Provider-observed power state of a Machine.
_Avoid_: Host readiness, online state, desired state

**Host Readiness**:
The freshness-qualified ability of a Host to accept management and Session work.
_Avoid_: Infrastructure State, reachability, Session occupancy

**Session Occupancy**:
The Host-attested capacity and active Session use of a Host.
_Avoid_: Host Readiness, online state, Lease

**Route State**:
The current evidence for direct, Beacon-assisted, and Relay paths between a Client Device and Host.
_Avoid_: Host Availability, network status, relay allocation

**Wake Ticket**:
A short-lived authorization for one Beacon to wake one Machine for one approved intent.
_Avoid_: Grant, Relay ticket, magic packet

**Relay Allocation**:
A short-lived, quota-bound association between one Client Device, one Host, and one Constellation Relay.
_Avoid_: Session, Grant, public proxy

**Deployment Epoch**:
The Organization-approved identity generation for one Constellation Deployment. Restore fencing prevents two active Deployments from sharing one epoch.
_Avoid_: schema version, release version, backup ID
