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

## Template: Blank Timeline

| Timestamp (UTC) | Log Source | Event ID / Type | Description | Analyst Notes |
|---|---|---|---|---|
| [Add] | [Add] | [Add] | [Add] | [Add] |

---

## Example: Brute Force Scenario

> This is a structural example only. Replace with actual timestamps and data from your lab.

| Timestamp (UTC) | Log Source | Event ID / Type | Description | Analyst Notes |
|---|---|---|---|---|
| [T+0:00] | Linux auth.log | SSH Failed password | First failed login attempt for user root from 192.168.56.10 | Start of brute force activity |
| [T+0:01] | Linux auth.log | SSH Failed password | Continued failed attempts — high frequency | Pattern consistent with automated tool |
| [T+0:03] | Wazuh | Rule 5712 | Brute force threshold alert triggered | Alert fired — investigation begins |
| [T+0:05] | Linux auth.log | SSH Accepted password | Successful login for user [account] from 192.168.56.10 | Possible credential compromise |
| [T+0:06] | Linux auth.log | Sudo usage | sudo command executed by [account] | Post-compromise privilege escalation |

[Replace this example with your actual lab timeline data.]

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
- Splunk: use `| sort _time` and `| timechart` for visual timelines
- Optional: Timesketch, Plaso (for more complex investigations)
