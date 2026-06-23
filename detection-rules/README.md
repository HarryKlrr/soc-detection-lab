# Detection Rules

This section contains detection rules and queries developed during this lab project. Rules are organised by platform.

## Contents

| File | Description |
|---|---|
| [wazuh-rules.md](wazuh-rules.md) | Wazuh rule coverage per scenario — stock rules confirmed firing, plus one custom rule attempt |
| [sigma-rules.md](sigma-rules.md) | Sigma-style generic detection rules |

## Status

All nine scenarios have been checked against live Wazuh data. Seven are covered by stock rules with no custom rule needed, Nmap isn't applicable since Wazuh is a HIDS, and the scheduled-task persistence rule (Sysmon-based, custom) still doesn't fire — left as an open finding in `wazuh-rules.md` rather than papered over.

Sigma rules in `sigma-rules.md` remain untested templates.

Splunk and YARA were planned for this lab but never actually built or tested, so they've been left out of this section to keep things accurate to what was actually implemented.
