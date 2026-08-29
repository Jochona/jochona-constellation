---
status: accepted
---

# Start with a power-only Proxmox Provider

The first Provider adapter manages registered Proxmox QEMU virtual machines and only reads state, starts, requests graceful shutdown, and reads task outcomes. It never provisions or reconfigures infrastructure.

This narrow scope proves lifecycle safety against real hardware before additional Providers enlarge the seam. It also keeps Provider billing and resource ownership with the operator.
