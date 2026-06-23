# Network Indicators Reference

## Overview

This document summarises key network-based indicators used during investigation of the lab scenarios.

---

## Indicators by Scenario

### Scenario 1 — Nmap Port Scan

| Indicator Type | Value | Notes |
|---|---|---|
| Source IP | 192.168.56.10 | Kali Linux VM on host-only network |
| Target IP | 192.168.56.20 | Windows 10 target VM |
| Indicator | `nmap -Pn` and `nmap -Pn -sV` against the target — 3 ports found open (135, 139, 445) | Standard Nmap scan, no ping discovery (ICMP blocked by Windows Firewall) |

No NIDS or firewall log evidence was captured for this scenario — Wazuh (a HIDS) generated no alert, which is itself the key finding. See [`../attack-scenarios/nmap-scan-detection.md`](../attack-scenarios/nmap-scan-detection.md).

---

### Scenario 2 — Brute Force

| Indicator Type | Value | Notes |
|---|---|---|
| Source | localhost (192.168.56.20) | Simulated locally via PowerShell against a local account — not a network-based attack from Kali, so there is no attacker source IP or target port for this scenario |
| Indicator | 21 x Event ID 4625 in ~10 seconds against local account `labvictim`, Logon Type 2 (interactive) | High-frequency failure rate is what flags it as automated |

See [`../incident-reports/IR-001-brute-force.md`](../incident-reports/IR-001-brute-force.md) for full evidence.

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
| Windows Firewall | Windows Security Event Log | Not enabled in this lab |
| Linux UFW / iptables | `/var/log/ufw.log` or syslog | N/A — Linux target never built |
| Wazuh Network alerts | Wazuh Manager | Wazuh dashboard / API |
| Wireshark PCAP | Local capture on host-only interface | Manual review |

Windows Firewall connection logging was never enabled in this lab (see the Remediation Recommendations in [`../incident-reports/IR-005-nmap-scan.md`](../incident-reports/IR-005-nmap-scan.md)), and the planned Linux target VM was never built (see `../lab-setup/virtual-machines.md`). Every detection in this lab therefore runs on host-based telemetry via the Wazuh agent (Windows Security log, Sysmon, Defender Operational log, Task Scheduler Operational log) — there is no network-level (NIDS/firewall) log source currently forwarded.
