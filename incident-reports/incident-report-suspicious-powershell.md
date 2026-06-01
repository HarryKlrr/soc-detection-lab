# Incident Report — Suspicious PowerShell Execution

**Report Reference:** IR-[Add sequential ID]-[Year]
**Date Created:** [Add date]
**Analyst:** John
**Severity:** [Add: Low / Medium / High / Critical]
**Status:** 🔄 In Progress

---

## 1. Executive Summary

[Provide a 2–3 sentence non-technical summary of the incident suitable for a manager or stakeholder. Include: what was detected, when, and what action was taken.]

---

## 2. Incident Overview

| Field | Detail |
|---|---|
| **Incident Type** | Suspicious PowerShell execution with encoded or bypass arguments |
| **MITRE Technique** | T1059.001 — Command and Scripting Interpreter: PowerShell |
| **Affected Host** | [Add hostname / IP] |
| **Affected Account** | [Add if applicable] |
| **Detection Time** | [Add timestamp UTC] |
| **Lab Environment** | Local isolated virtual network |

---

## 3. Detection Source

**Platform:** Wazuh (Sysmon integration) / Splunk

**Alert / Rule Triggered:** [Add the name or ID of the rule or alert that triggered this investigation]

**Initial Indicator:** [Add description of the initial indicator — e.g., "Wazuh alert: X failed logon events within 60 seconds from 192.168.56.10"]

[Insert screenshot of triggered alert here]

---

## 4. Timeline of Events

| Timestamp (UTC) | Event | Log Source | Notes |
|---|---|---|---|
| [Add] | [Add first observed event] | [Add] | [Add] |
| [Add] | [Add subsequent events] | [Add] | [Add] |
| [Add] | [Add detection trigger] | [Add] | [Add] |
| [Add] | [Add response action taken] | [Add] | [Add] |

---

## 5. Indicators Observed

| Indicator Type | Value | Notes |
|---|---|---|
| Source IP | [Add] | [Add context] |
| Target IP / Host | [Add] | [Add context] |
| Windows Event IDs | Sysmon Event ID 1, Windows Event ID 4688 | [Add] |
| Account Name | [Add if applicable] | [Add] |
| Process / Command | [Add if applicable] | [Add] |

---

## 6. Investigation Notes

[Document the investigation steps taken, findings at each step, and analytical judgements made. Include references to specific log entries reviewed.]

Key questions addressed:
- What was the source of the activity?
- Was the activity successful or blocked?
- What systems or accounts were affected?
- Is there evidence of further activity following this event?

[Add your investigation findings here.]

---

## 7. Containment Actions

- Identified and terminated the suspicious PowerShell process (lab simulation)
- Reviewed all child processes and network connections spawned by the session
- Checked for persistence mechanisms created during the session

[Add the specific containment actions taken in the lab. For a portfolio lab, describe what you would do in a real environment if this were a genuine incident.]

---

## 8. Remediation Recommendations

[List recommended remediation steps to prevent recurrence. Be specific and actionable.]

- [Add recommendation 1]
- [Add recommendation 2]
- [Add recommendation 3]

---

## 9. Lessons Learned

[Reflect on this incident from a detection and response perspective.]

- What worked well?
- What could be detected earlier or more accurately?
- What defensive control would have prevented or reduced the impact?
- What would you do differently?

[Add your reflections here.]

---

## 10. Evidence / Screenshots

[List all evidence items collected. Add screenshots below.]

| # | Evidence Item | Source | Notes |
|---|---|---|---|
| 1 | [Add description] | [Add log/tool] | [Add] |
| 2 | [Add description] | [Add log/tool] | [Add] |

[Insert screenshots here]

---

## MITRE ATT&CK Reference

**Technique:** https://attack.mitre.org/techniques/T1059/001/

[Add link to the relevant ATT&CK page and confirm the mapping once your lab scenario is complete.]

---

*Report prepared as part of the SOC Detection Lab portfolio project. All activity was performed in a private, isolated, locally hosted lab environment.*
