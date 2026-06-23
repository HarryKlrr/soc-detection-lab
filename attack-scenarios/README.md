# Attack Scenarios

This section documents each simulated attack scenario performed in the local lab environment. All activity was conducted exclusively on isolated virtual machines with no connection to external networks.

## Scenarios

| # | Scenario | MITRE Technique | Status |
|---|---|---|---|
| 1 | [Nmap Port Scan Detection](nmap-scan-detection.md) | T1046 — Network Service Discovery | ✅ Complete |
| 2 | [Failed Login / Brute Force](failed-login-bruteforce.md) | T1110 — Brute Force | ✅ Complete |
| 3 | [Suspicious PowerShell](suspicious-powershell.md) | T1059.001 — PowerShell | ✅ Complete |
| 4 | [New Admin Account Created](new-admin-account-created.md) | T1136 — Create Account | ✅ Complete |
| 5 | [Windows Defender Disabled](defender-disabled.md) | T1562.001 — Disable or Modify Tools | ✅ Complete |

> Note: scenarios 6–9 (log clearing, scheduled task persistence, phishing, malware hash triage) were documented directly as formal incident reports rather than informal scenario write-ups — see [`../incident-reports/`](../incident-reports/).
