# Draft Rules

Detection logic written but not yet validated against live data — kept separate from [`../detection-rules/`](../detection-rules/) so nothing in here gets mistaken for tested, production-ready coverage.

## Contents

| File | Description |
|---|---|
| [sigma-rules.md](sigma-rules.md) | Sigma-format rules for the PowerShell, admin-account, and Defender scenarios — written and `status: experimental`, never run through `sigmac`/pySigma or checked against live Wazuh output |

Everything that's actually been confirmed firing against real lab data lives in [`../detection-rules/wazuh-rules.md`](../detection-rules/wazuh-rules.md).
