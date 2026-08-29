---
status: accepted
---

# Use explicit Leases and separate state axes

Constellation keeps desired action, infrastructure, Host, Session, and route facts separate. Principal-scoped Leases and Holds, not input activity or one synthetic status, control automatic shutdown.

This model requires richer Projections and reconciliation. It prevents stale reachability or a lost heartbeat from becoming destructive proof that a Machine is idle.
