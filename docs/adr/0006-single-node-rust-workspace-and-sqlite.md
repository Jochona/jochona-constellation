---
status: accepted
---

# Use one Rust workspace and SQLite for the initial appliance

The repository contains one Rust workspace, the `constellationd` and `jochona-relay` binaries, and one TypeScript web UI. A single-node Deployment stores authoritative state in SQLite WAL.

This shape favors owner operation, atomic local transactions, and one provenance chain over early distributed infrastructure. Restore epochs provide fencing instead of unsupported active-active behavior.
