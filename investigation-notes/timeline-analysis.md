# Timeline Analysis

## Overview

Building a chronological timeline of events is one of the most valuable techniques in incident investigation. A timeline allows an analyst to understand the sequence of attacker actions, identify gaps, and communicate findings clearly.

---

## Timeline Structure

A standard investigation timeline includes the following columns:

| Timestamp | Source | Event | Description | Significance |
|---|---|---|---|---|
| [Date/Time UTC] | [Log source] | [Event ID / type] | [What happened] | [Why it matters] |

---

## Example: Brute Force Scenario (Real Lab Data)

Actual timeline from [`../incident-reports/IR-001-brute-force.md`](../incident-reports/IR-001-brute-force.md):

| Timestamp (UTC) | Log Source | Event ID / Type | Description | Analyst Notes |
|---|---|---|---|---|
| 2026-06-08 17:26:46 | Windows Security Log | 4625 | First failed logon for `labvictim` | Logon Type 2 (interactive) |
| 2026-06-08 17:26:46–17:26:51 | Windows Security Log | 4625 (x20 more) | 21 total failures in ~10 seconds | Speed/volume indicates a script, not a typo |
| 2026-06-08 17:26:48 | Wazuh | Rule 60204 | "Multiple Windows Logon Failures" alert fired | Level 10 — this is what drew analyst attention |
| 2026-06-08 17:26:51 | Windows Security Log | 4624 (absent) | No successful logon followed the failures | Confirms the attack did not succeed |
| 2026-06-08 17:27:00 | Windows — `net user` | — | Account `labvictim` disabled | Containment |
| 2026-06-08 17:30:00 | Windows — `net accounts` | — | Lockout threshold set to 5 attempts | Remediation |

---

## Tips for Building an Effective Timeline

- Always use UTC timestamps to avoid timezone confusion
- Include the log source for each event so findings can be traced back to evidence
- Note the first and last observed times for each activity type
- Highlight events that represent decision points — e.g., the first successful logon after failures
- Cross-reference multiple log sources to fill gaps
- Keep analyst notes separate from factual observations

---

## Tools

- Manual: spreadsheet or markdown table
- Wazuh: filter alerts by time range in the dashboard
- Optional: Timesketch, Plaso (for more complex investigations)
