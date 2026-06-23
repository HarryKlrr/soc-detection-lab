# Sample Logs

This directory contains sanitised and anonymised log samples collected from the lab during attack simulations.

> **Important:** Do not commit real log files containing sensitive information such as real IP addresses, hostnames, usernames, or other personally identifiable data. Sanitise all logs before committing. Replace real values with clearly marked placeholders (e.g., `[REDACTED-IP]`, `[HOSTNAME]`).

## Contents

All samples below are saved flat in this directory (not split into subfolders) and are cross-linked from their corresponding incident report's Evidence section.

| File | Source | Incident Report |
|---|---|---|
| `60204-bruteforce-alert.json` | Wazuh alert export — rule 60204 (Event ID 4625) | [IR-001](../incident-reports/IR-001-brute-force.md) |
| `92057-powershell-encoded-alert.json` | Wazuh alert export — rule 92057 (Sysmon Event ID 1) | [IR-002](../incident-reports/IR-002-suspicious-powershell.md) |
| `60154-admin-account-alert.json` | Wazuh alert export — rule 60154 (Event ID 4732) | [IR-003](../incident-reports/IR-003-new-admin-account.md) |
| `5007-defender-disabled-sample.xml` | Windows Event Viewer export — Event ID 5007 | [IR-004](../incident-reports/IR-004-defender-disabled.md) |
| `63103-logcleared-alert.json` | Wazuh alert export — rule 63103 (Event ID 1102, 2026-06-21 retest) | [IR-006](../incident-reports/IR-006-log-clearing.md) |
| `60228-scheduledtask-4698-alert.json` | Wazuh alert export — rule 60228 (Event ID 4698) | [IR-007](../incident-reports/IR-007-scheduled-task.md) |
| `sysmon1-schtasks-persistence-sample.xml` | Sysmon Event ID 1 export — `schtasks.exe` persistence | [IR-007](../incident-reports/IR-007-scheduled-task.md) |
| `sysmon1-powershell-encoded-sample.xml` | Sysmon Event ID 1 export — `-EncodedCommand` process (re-exported 2026-06-23) | [IR-002](../incident-reports/IR-002-suspicious-powershell.md) |

## Adding More Logs

1. Export the relevant log entries from your SIEM or log source
2. Sanitise sensitive fields
3. Save to this directory with a descriptive filename (e.g., `4625-brute-force-sample.xml`)
4. Reference the file in the relevant attack scenario or incident report's Evidence section
