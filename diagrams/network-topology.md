# Lab Network Topology

Host-only VirtualBox network, isolated from the internet and from the host machine's primary LAN.

```mermaid
flowchart TB
    subgraph HOST["VirtualBox Host-Only Network — 192.168.56.0/24"]
        KALI["Kali Linux<br/>192.168.56.10<br/>Attack simulation<br/>4 GB RAM / 80 GB disk"]
        WIN["Windows10-Target<br/>192.168.56.20<br/>Target endpoint<br/>4 GB RAM / 60 GB disk<br/>Sysmon + Wazuh Agent"]
        LINUX["Linux Target<br/>192.168.56.30<br/>PLANNED — never built<br/>Ubuntu 22.04 LTS"]
        WAZUH["Wazuh Manager<br/>192.168.56.101<br/>SIEM / HIDS<br/>4 GB RAM / 50 GB disk<br/>Manager + Dashboard"]
    end

    KALI -->|"Nmap scan, simulated attacks"| WIN
    WIN -->|"Agent forwards: Security log, Sysmon,<br/>Defender Operational, TaskScheduler Operational"| WAZUH
    LINUX -.->|"Never deployed — no agent, no traffic"| WAZUH
```

## Notes

- **Hypervisor:** VirtualBox, host-only adapter — no internet egress, isolated from the host's primary LAN.
- **Linux Target (192.168.56.30):** planned in the original design (see [`network-design.md`](network-design.md)) but never built. Shown here only to document original intent — no scenario in this lab touches it.
- **Splunk:** also planned and never deployed. This is a Wazuh-only detection stack — see [`network-design.md`](network-design.md) for the full history of what was planned vs. what was actually built.
- VM specs above reflect Wazuh's documented minimum sizing for a single-node deployment and standard VirtualBox defaults for Kali/Windows 10 guests — see [`virtual-machines.md`](virtual-machines.md).
