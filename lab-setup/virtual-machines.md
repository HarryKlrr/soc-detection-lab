# Virtual Machines

## VM Summary

| VM | OS | Role | RAM | Storage | IP |
|---|---|---|---|---|---|
| Kali Linux | Kali Rolling | Attack simulation | [Add] | [Add] | 192.168.56.10 |
| Windows Target | Windows 10/11 | Target endpoint | [Add] | [Add] | 192.168.56.20 |
| Linux Target | Ubuntu 22.04 LTS | Target endpoint | [Add] | [Add] | 192.168.56.30 |
| Wazuh Manager | Ubuntu 22.04 LTS | SIEM / HIDS | [Add] | [Add] | 192.168.56.40 |
| Splunk | Ubuntu 22.04 LTS | SIEM | [Add] | [Add] | 192.168.56.50 |

> Replace placeholder values with your actual VM specifications.

## Hypervisor

[Add your hypervisor details here — e.g., VirtualBox 7.x / VMware Workstation Pro x.x]

## Snapshot Strategy

Snapshots are taken at the following points:
- Clean OS install (before any tools)
- Post-tool installation (before any attack simulation)
- After each scenario (for rollback)

---

*[Add screenshots of your VM manager interface here — see `screenshots/`]*
