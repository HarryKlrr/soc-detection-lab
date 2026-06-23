# Virtual Machines

## VM Summary

| VM | OS | Role | RAM | Storage | IP |
|---|---|---|---|---|---|
| Kali Linux | Kali Rolling | Attack simulation | 4 GB | 80 GB | 192.168.56.10 |
| Windows Target | Windows 10/11 | Target endpoint | 4 GB | 60 GB | 192.168.56.20 |
| Linux Target | Ubuntu 22.04 LTS | Target endpoint — planned, not yet built | N/A — never built | N/A — never built | 192.168.56.30 |
| Wazuh Manager | Ubuntu 22.04 LTS | SIEM / HIDS | 4 GB | 50 GB | 192.168.56.101 |

> Splunk was planned for this lab but never deployed — see `network-design.md`. The Linux target VM was also never built; no scenario in this lab uses it, which is why it has no RAM/Storage allocation. RAM/Storage above reflect Wazuh's documented minimum sizing for a single-node deployment (4 GB RAM / 50 GB disk) and standard VirtualBox defaults for Kali and Windows 10 guests.

## Hypervisor

VirtualBox

## Snapshot Strategy

Snapshots are taken at the following points:
- Clean OS install (before any tools)
- Post-tool installation (before any attack simulation)
- After each scenario (for rollback)

---

*[Add screenshots of your VM manager interface here — see `screenshots/`]*
