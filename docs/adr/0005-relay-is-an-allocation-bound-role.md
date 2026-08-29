---
status: accepted
---

# Make Relay an allocation-bound Constellation role

Constellation Relay is a separate deployment role, not another product or a general proxy. It forwards only authenticated allocations and cannot grant access or control Machines.

Jochona native full GameStream encryption remains mandatory across the Relay path. Relay operators can observe transport metadata, so the product discloses that exposure.
