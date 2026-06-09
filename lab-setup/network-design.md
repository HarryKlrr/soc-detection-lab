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

> Ubuntu target and Splunk to be added in a later phase.

## Network Isolation

- All VMs are connected to a **host-only** adapter in VirtualBox/VMware
- No NAT or bridged adapters are used on attack VMs
- Internet access for tooling downloads is done via a separate NAT adapter on non-attack VMs only, which is disabled before any attack simulation begins

## Firewall Notes

[Add notes on any host firewall rules or VM firewall configurations here.]

---

*[Add a screenshot of your network adapter settings here — see `screenshots/`]*
