# SOC Detection Lab

> A home lab project simulating real-world Security Operations Centre (SOC) workflows — built to demonstrate Blue Team skills for entry-level SOC Analyst and Junior Cyber Security Analyst roles.

---

## ⚠️ Ethical & Legal Notice

All activity documented in this repository was performed **exclusively within a private, isolated, locally hosted lab environment**. No scans, attacks, or simulations were directed at any external system, public network, or device without explicit authorisation. This project exists solely for educational and professional development purposes. All techniques are documented in alignment with responsible disclosure and ethical security research principles.

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
| **SIEM Platforms** | Wazuh, Splunk |
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
│  ┌──────────────────┐   ┌───────────────────────┐   │
│  │  Wazuh Manager   │   │    Splunk (SIEM)       │   │
│  │  + Dashboard     │   │    + Forwarder         │   │
│  └──────────────────┘   └───────────────────────┘   │
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
| **Splunk** | SIEM — log ingestion, search, and dashboards |
| **Sysmon** | Enhanced Windows process and event telemetry |
| **Windows Event Viewer** | Native Windows log inspection |
| **Linux auth logs** | `/var/log/auth.log`, `/var/log/secure` |
| **Wireshark** *(optional)* | Packet capture and network traffic analysis |

---

## Attack Scenarios

| # | Scenario | Technique Simulated | Logs Collected | Detection Method | Status | Link |
|---|---|---|---|---|---|---|
| 1 | Nmap Port Scan | Network reconnaissance | Wazuh network alerts, firewall logs | Wazuh rule / Splunk SPL | 🔄 In Progress | [View](attack-scenarios/nmap-scan-detection.md) |
| 2 | Failed Login / Brute Force | Credential attack (T1110) | Windows Event 4625 | Wazuh built-in authentication_failed rule | ✅ Complete | [View](attack-scenarios/failed-login-bruteforce.md) |
| 3 | Suspicious PowerShell | Execution via PowerShell (T1059.001) | Sysmon Event 1, Windows Event 4688 | Process creation rule | 🔄 In Progress | [View](attack-scenarios/suspicious-powershell.md) |
| 4 | New Admin Account Created | Persistence — account creation (T1136) | Windows Event 4720, 4728 | Account creation alert | 🔄 In Progress | [View](attack-scenarios/new-admin-account-created.md) |
| 5 | Windows Defender Disabled | Defence evasion (T1562.001) | Windows Event 5007 | Defender config change rule | 🔄 In Progress | [View](attack-scenarios/defender-disabled.md) |

---

## Skills Demonstrated

- **SIEM Monitoring** — Configuring and monitoring alerts in Wazuh and Splunk
- **Log Analysis** — Parsing and interpreting Windows Event Logs and Linux auth logs
- **Windows Event Analysis** — Identifying malicious activity from key Windows Event IDs
- **Linux Log Analysis** — Reviewing `/var/log/auth.log` for suspicious authentication patterns
- **Incident Response** — Following a structured IR lifecycle from detection to remediation
- **Detection Engineering Basics** — Writing Wazuh rules, Splunk SPL queries, and Sigma-style rules
- **Alert Triage** — Prioritising and investigating triggered alerts
- **Report Writing** — Producing professional, structured incident reports
- **MITRE ATT&CK Mapping** — Mapping observed behaviours to ATT&CK tactics and techniques

---

## Portfolio Relevance

This project demonstrates practical readiness for an entry-level SOC Analyst or Junior Cyber Security Analyst role. It covers the core competencies that appear consistently in job descriptions for these positions:

- Hands-on experience with industry-standard SIEM platforms (Wazuh, Splunk)
- Practical knowledge of Windows and Linux log sources
- Ability to write and apply detection rules
- Structured approach to incident investigation and documentation
- Familiarity with the MITRE ATT&CK framework
- Professional written communication through incident reports and playbooks

Each scenario mirrors the type of alert a Tier 1 or Tier 2 SOC analyst would encounter and triage in a real environment.

---

## How to Navigate This Repository

```
soc-detection-lab/
├── lab-setup/              # Lab environment documentation and configuration guides
├── attack-scenarios/       # Step-by-step documentation of each simulated attack
├── detection-rules/        # Wazuh rules, Splunk SPL queries, and Sigma rules
├── investigation-notes/    # Reference notes on log sources and event IDs
├── incident-reports/       # Formal incident reports for each scenario
├── sample-logs/            # Anonymised/sanitised log samples from the lab
├── screenshots/            # Evidence screenshots from the lab
├── diagrams/               # Architecture and workflow diagrams
├── playbooks/              # Incident response playbooks
├── templates/              # Reusable templates for reports, rules, and notes
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

*Last updated: 2026 | Author: John | Role target: SOC Analyst / Junior Cyber Security Analyst*
