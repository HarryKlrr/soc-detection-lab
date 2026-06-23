# SOC Detection Lab

> A home lab project simulating real-world Security Operations Centre (SOC) workflows — built to demonstrate Blue Team skills for entry-level SOC Analyst and Junior Cyber Security Analyst roles.

---

## ⚠️ Ethical & Legal Notice

Everything in this repo happened inside a private, isolated lab on my own hardware — nothing here ever touched an external network or system.

---

## Introduction

This repository documents a personal cybersecurity home lab built to simulate the day-to-day workflows of a SOC analyst. It covers the full detection lifecycle: log collection, attack simulation in a controlled environment, alert detection, investigation, and incident response.

The lab is designed to produce portfolio-quality evidence of practical Blue Team skills, including SIEM configuration, detection rule authoring, log analysis, and professional incident reporting.

---

## Project Overview

| Property | Detail |
|---|---|
| **Focus Area** | Blue Team / SOC Analysis |
| **Skill Level** | Entry-level / Graduate |
| **Lab Type** | Local virtualised environment |
| **SIEM Platforms** | Wazuh |
| **OS Targets** | Windows 10/11, Ubuntu Linux |
| **Attack Source** | Kali Linux (isolated local network only) |

---

## Lab Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Host Machine                       │
│                                                     │
│  ┌─────────────┐   ┌──────────────┐   ┌─────────┐  │
│  │  Kali Linux │   │ Windows 10   │   │ Ubuntu  │  │
│  │ (Attacker)  │   │  (Target)    │   │ (Target)│  │
│  └──────┬──────┘   └──────┬───────┘   └────┬────┘  │
│         │                 │                │        │
│         └─────────────────┴────────────────┘        │
│                     Host-Only Network               │
│                                                     │
│  ┌──────────────────┐                                │
│  │  Wazuh Manager   │                                │
│  │  + Dashboard     │                                │
│  └──────────────────┘                                │
└─────────────────────────────────────────────────────┘
```

> See [`lab-setup/network-design.md`](lab-setup/network-design.md) for full architecture details.

---

## Tools Used

| Tool | Role |
|---|---|
| **Kali Linux** | Attack simulation (local lab only) |
| **Windows 10/11** | Target endpoint — Windows log source |
| **Ubuntu Linux** | Target endpoint — Linux log source |
| **Wazuh** | Open-source SIEM / HIDS / EDR |
| **Sysmon** | Enhanced Windows process and event telemetry |
| **Windows Event Viewer** | Native Windows log inspection |
| **Linux auth logs** | `/var/log/auth.log`, `/var/log/secure` |
| **Wireshark** *(optional)* | Packet capture and network traffic analysis |

---

## Attack Scenarios

| # | Scenario | Technique Simulated | Logs Collected | Detection Method | Status | Link |
|---|---|---|---|---|---|---|
| 1 | Nmap Port Scan | Network reconnaissance (T1046) | Kali Nmap output, HIDS limitation documented | NIDS required — Wazuh (HIDS) cannot detect network scans | ✅ Complete | [View](attack-scenarios/nmap-scan-detection.md) / [IR-005](incident-reports/IR-005-nmap-scan.md) |
| 2 | Failed Login / Brute Force | Credential attack (T1110) | Windows Event 4625 | Wazuh built-in authentication_failed rule | ✅ Complete | [View](attack-scenarios/failed-login-bruteforce.md) / [IR-001](incident-reports/IR-001-brute-force.md) |
| 3 | Suspicious PowerShell | Execution via PowerShell (T1059.001) | Sysmon Event 1, Windows Event 4688 | Wazuh built-in rule 92057 (level 12) | ✅ Complete | [View](attack-scenarios/suspicious-powershell.md) / [IR-002](incident-reports/IR-002-suspicious-powershell.md) |
| 4 | New Admin Account Created | Persistence — account creation (T1136) | Windows Event 4720, 4732 | Wazuh built-in rule 60154 (level 12) | ✅ Complete | [View](attack-scenarios/new-admin-account-created.md) / [IR-003](incident-reports/IR-003-new-admin-account.md) |
| 5 | Windows Defender Disabled | Defence evasion (T1562.001) | Windows Event 5007 | Event Viewer — Defender Operational log | ✅ Complete | [View](attack-scenarios/defender-disabled.md) / [IR-004](incident-reports/IR-004-defender-disabled.md) |
| 6 | Audit Log Cleared | Defence evasion — log clearing (T1070.001) | Windows Security Event 1102 | Wazuh built-in rule 60106 + custom audit policy fix | ✅ Complete | [IR-006](incident-reports/IR-006-log-clearing.md) |
| 7 | Suspicious Scheduled Task | Persistence (T1053.005) | Windows Event 4698, 106 | Custom Wazuh rule (Sysmon-based) + TaskScheduler/Operational log | ✅ Complete | [IR-007](incident-reports/IR-007-scheduled-task.md) |
| 8 | Phishing Email (Brand Impersonation) | Phishing — spearphishing link (T1566.002) | Sourced email sample — static triage | Manual analyst triage (sender/domain analysis) | ✅ Complete | [IR-008](incident-reports/IR-008-phishing-email.md) |
| 9 | Malware Hash Triage | Malicious file execution (T1204.002) | Sourced sample hash — OSINT triage | Manual OSINT triage (MalwareBazaar, VirusTotal) | ✅ Complete | [IR-009](incident-reports/IR-009-malware-hash-triage.md) |

---

## Skills Demonstrated

- **SIEM Monitoring** — Configuring and monitoring alerts in Wazuh
- **Log Analysis** — Parsing and interpreting Windows Event Logs and Linux auth logs
- **Windows Event Analysis** — Identifying malicious activity from key Windows Event IDs
- **Linux Log Analysis** — Reviewing `/var/log/auth.log` for suspicious authentication patterns
- **Incident Response** — Following a structured IR lifecycle from detection to remediation
- **Detection Engineering Basics** — Writing and testing custom Wazuh rules, and drafting Sigma-style rules
- **Alert Triage** — Prioritising and investigating triggered alerts
- **Report Writing** — Producing professional, structured incident reports
- **MITRE ATT&CK Mapping** — Mapping observed behaviours to ATT&CK tactics and techniques

Each scenario mirrors the kind of alert a Tier 1 or Tier 2 SOC analyst would actually triage on the job.

---

## How to Navigate This Repository

```
soc-detection-lab/
├── lab-setup/              # Lab environment documentation and configuration guides
├── attack-scenarios/       # Step-by-step documentation of each simulated attack
├── detection-rules/        # Wazuh rules and Sigma rules
├── investigation-notes/    # Reference notes on log sources and event IDs
├── incident-reports/       # Formal incident reports for each scenario
├── sample-logs/            # Anonymised/sanitised log samples from the lab
├── screenshots/            # Evidence screenshots from the lab
├── diagrams/               # Architecture and workflow diagrams
├── playbooks/              # Incident response playbooks
└── assets/                 # Supporting assets
```

Start with [`lab-setup/README.md`](lab-setup/README.md) for environment context, then explore individual attack scenarios under [`attack-scenarios/`](attack-scenarios/).

---

## Status Legend

| Symbol | Meaning |
|---|---|
| ✅ | Complete — evidence collected and documented |
| 🔄 | In Progress |
| 📋 | Planned — not yet started |

---

*Last updated: 23 June 2026 | Author: Harry (GitHub: [harryklrr](https://github.com/harryklrr)) | Role target: SOC Analyst / Junior Cyber Security Analyst*
