# Network Indicators Reference

## Overview

This document summarises key network-based indicators used during investigation of the lab scenarios.

---

## Indicators by Scenario

### Scenario 1 — Nmap Port Scan

| Indicator Type | Value | Notes |
|---|---|---|
| Source IP | [Add lab attacker IP] | Kali Linux VM on host-only network |
| Target IP | [Add lab target IP] | Windows/Linux target VM |
| Indicator | High rate of TCP SYN packets to multiple ports | Characteristic of a TCP SYN scan |
| Indicator | ICMP probes followed by port connections | Typical of a default Nmap scan |

[Add network capture or firewall log evidence here.]

---

### Scenario 2 — Brute Force

| Indicator Type | Value | Notes |
|---|---|---|
| Source IP | [Add lab attacker IP] | Kali Linux VM |
| Target port | 22 (SSH) / 3389 (RDP) | Depends on scenario variant |
| Indicator | Repeated authentication attempts from single IP | High 4625 / auth.log failure rate |

[Add evidence here.]

---

## General IOC Categories

| Category | Examples |
|---|---|
| IP Addresses | Attacker source IPs, C2 server addresses |
| Ports | Unusual outbound connections, non-standard service ports |
| Protocols | Cleartext credentials over Telnet or FTP |
| DNS | Unusual or newly registered domains, DGA patterns |
| User Agents | Unusual or tool-generated HTTP user agents |
| Volume | Abnormally high connection rates, data transfer volumes |

---

## Network Log Sources in This Lab

| Source | Location | Captured Via |
|---|---|---|
| Windows Firewall | Windows Security Event Log | Wazuh agent / Splunk forwarder |
| Linux UFW / iptables | `/var/log/ufw.log` or syslog | Wazuh agent / Splunk forwarder |
| Wazuh Network alerts | Wazuh Manager | Wazuh dashboard / API |
| Wireshark PCAP | Local capture on host-only interface | Manual review |

[Add notes on network log configuration in your specific lab here.]
