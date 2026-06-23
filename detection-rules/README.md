# Detection Rules

This section contains detection rules and queries developed during this lab project. Rules are organised by platform.

## Contents

| File | Description |
|---|---|
| [wazuh-rules.md](wazuh-rules.md) | Wazuh rule coverage per scenario — stock rules confirmed firing, plus one custom rule attempt |
| [sigma-rules.md](sigma-rules.md) | Sigma-style generic detection rules |

## Status

Wazuh rule coverage has been confirmed for the scenarios where a custom rule turned out to be unnecessary (stock rules 60228, 63103, etc. — see `wazuh-rules.md`) and is still a `[PLACEHOLDER]` for scenarios not yet revisited. One custom rule (scheduled-task persistence via Sysmon) was written and tested but never fired live — documented as an open finding rather than a working detection.

Sigma rules in `sigma-rules.md` remain untested templates.

> Splunk and YARA were planned for this lab but never actually built or tested — they have been removed from this section to keep the documentation accurate to what was implemented.

> Rules marked `[PLACEHOLDER]` have not yet been tested. Do not use in a production environment.
