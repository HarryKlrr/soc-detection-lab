# Network Design

## Overview

All lab machines are isolated on a host-only virtual network. No traffic from this network is routed to the internet or any external system.

## Network Topology

```
Host-Only Network: 192.168.56.0/24

┌──────────────────────────────────────────────────────┐
│  192.168.56.10   Kali Linux       (Attack VM)        │
│  192.168.56.20   Windows 10       (Target)           │
│  192.168.56.101  Wazuh Manager    (SIEM/HIDS)        │
└──────────────────────────────────────────────────────┘
```

> Ubuntu target was planned but never built — no scenario in this lab uses it. Splunk was also planned but never deployed; all detection in this lab runs on Wazuh.

## Network Isolation

- All VMs are connected to a **host-only** adapter in VirtualBox/VMware
- No NAT or bridged adapters are used on attack VMs
- Internet access for tooling downloads is done via a separate NAT adapter on non-attack VMs only, which is disabled before any attack simulation begins

## Firewall Notes

Host-only adapters have no external attack surface, so firewall rules here are minimal. `Windows10-Target` was left on the default Windows Defender Firewall configuration — no inbound rule was needed since the Wazuh agent always initiates outbound traffic to the manager (TCP/UDP 1514 for events, TCP 1515 for enrollment). On the Wazuh Manager, `ufw` was left disabled for this lab since the host-only network has no untrusted members; in a real deployment it would need explicit allow rules for 1514/1515 (agent traffic) and 443 (dashboard).
