# Incident Report — Security Event Log Cleared

**Incident ID:** IR-006-2026
**Date Created:** 2026-06-17
**Analyst:** Harry
**Severity:** High
**Status:** Closed

---

## 1. Executive Summary

On 17 June 2026, the Security event log on `Windows10-Target` (192.168.56.20) was cleared using `wevtutil cl Security` — a classic anti-forensics move attackers use to erase evidence after gaining access. Clearing a log doesn't go unnoticed: Windows logs a fresh Event ID 1102 the moment the log re-opens empty, so the action itself leaves a trace even as it destroys everything before it. I found the 1102 entry in Event Viewer, but it never showed up in Wazuh — a detection gap that's arguably more significant than the simulated attack itself, since this is exactly the kind of activity a SOC needs to catch every time.

---

## 2. Incident Overview

| Field | Detail |
|---|---|
| **Incident Type** | Anti-Forensics — Security Log Cleared |
| **MITRE Technique** | T1070.001 — Indicator Removal: Clear Windows Event Logs |
| **Affected Host** | Windows10-Target — 192.168.56.20 |
| **Actor** | `harry` (administrator, elevated CMD) |
| **Detection Time** | 2026-06-17 14:02 UTC (Event Viewer only — no Wazuh alert) |
| **Environment** | Isolated home lab — host-only network 192.168.56.0/24 |

---

## 3. Detection Source

**Platform:** Windows Event Viewer — Security log
**Event ID:** 1102 — "The audit log was cleared"
**SIEM alert:** None — searched Wazuh Threat Hunting for Event ID 1102, nothing returned
**Why:** Covered in Investigation Notes. This isn't a forwarding problem — the Security channel already reaches Wazuh fine for other event IDs (4625, 4720, 4732 all fired in earlier reports). The gap is specific to this event.

---

## 4. Timeline of Events

| Timestamp | Event | Source | Notes |
|---|---|---|---|
| 2026-06-17 14:00 | Security log cleared: `wevtutil cl Security` | Elevated CMD | Run as `harry` (administrator) |
| 2026-06-17 14:00 | Event ID 1102 logged | Windows Security Log | Logged immediately as the log re-opens empty |
| 2026-06-17 14:02 | Event Viewer checked | Windows Event Viewer | 1102 confirmed as effectively the only entry left |
| 2026-06-17 14:05 | Wazuh Threat Hunting searched for Event ID 1102 | Wazuh | No alert, no raw event returned |
| 2026-06-17 14:10 | Gap documented | — | A cleared log can't be un-cleared — containment here means closing the detection gap, not reversing the action |

---

## 5. Indicators Observed

| Indicator Type | Value | Notes |
|---|---|---|
| Affected Host | Windows10-Target — 192.168.56.20 | Local Windows 10 endpoint |
| Event ID | 1102 | "The audit log was cleared" |
| Tool | `wevtutil.exe` | Built-in Windows utility — no third-party tooling needed |
| Actor | `harry` | Administrator account, elevated prompt |
| Log Channel | Security | Same channel that already forwards 4625/4720/4732 successfully |

---

## 6. Investigation Notes

**Step 1 — Ran the simulated action**
`wevtutil cl Security` from an elevated CMD. This is the exact command (or its `Clear-EventLog` PowerShell equivalent) real attackers run right after achieving whatever they came in to do, to erase the trail that would show how they got there.

**Step 2 — Confirmed in Event Viewer**
Opened the Security log directly — Event ID 1102 was sitting there as essentially the only entry. Everything that happened before the clear is gone. That's the actual damage in this scenario: it's not just that an event fired, it's that the history behind it no longer exists.

**Step 3 — Checked Wazuh**
Searched Threat Hunting for Event ID 1102. Nothing — no alert, no raw record at all. That's the part worth sitting with: the Security channel is already proven to forward correctly, since 4625/4720/4732 all reached Wazuh in previous incidents. So this isn't a channel configuration gap like the Defender log in IR-004 — the pipe is open, the alert just never fires.

**Step 4 — Most likely explanation**
Wazuh's manager only retains events that match a rule in the ruleset — anything that doesn't match gets decoded and then dropped, unless the archives module (`logall_json`) is switched on. If there's no default rule watching specifically for Event ID 1102, the event never becomes searchable, alert or otherwise. The fix is either enabling archives so raw events are retained regardless of rule matches, or adding a dedicated local rule for 1102.

**Step 5 — Assessed severity**
An attacker clearing logs almost always means they've already done something they don't want found. On its own, Event ID 1102 should be one of the highest-priority alerts a SOC has, because by the time it fires, whatever evidence came before it is already gone.

**Conclusion:** True positive on the simulated action — log cleared, 1102 confirmed in Event Viewer. Detection gap confirmed and documented. Same theme running through this lab as IR-004 and IR-005: a log entry existing and a SIEM alerting on it are two different things.

---

## 7. Containment Actions

- No way to reverse a cleared log — the cleared data is unrecoverable from this host
- Documented the detection gap
- Confirmed the Security channel itself is healthy (other event IDs from the same channel still forward correctly)

---

## 8. Remediation Recommendations

- Add a dedicated Wazuh rule for Event ID 1102 — given what it represents, it should be one of the highest-severity rules in the local ruleset
- Enable the archives module (`logall_json`) on the Wazuh manager so events without a matching rule are still retained and searchable, instead of silently dropped
- Restrict who can clear the Security log to break-glass admin accounts only, and monitor use of that access itself
- Forward logs to a second, independent collector so a local clear doesn't destroy the only copy of the evidence

---

## 9. Lessons Learned

- Event ID 1102 is more dangerous in what it implies than in itself — it's a sign something else already happened and got erased
- The Security channel forwarding correctly for other event IDs doesn't mean every event ID in that channel gets alerted on — channel forwarding and ruleset coverage are two different things, and this is the clearest example of that gap in this lab so far
- If a SIEM has no default rule for something this consequential, that's a tuning gap that needs fixing, not something to shrug off
- Silence from the SIEM on a high-value event ID should be treated as suspicious as an actual alert firing

---

## 10. Evidence

| # | Evidence Item | Source |
|---|---|---|
| 1 | `event-1102-log-cleared.png` | Windows Event Viewer — Event ID 1102 |
| 2 | `wazuh-1102-no-results.png` | Wazuh Threat Hunting — search for Event ID 1102 returning no results |

![Windows Event Viewer — Event ID 1102 log cleared](../screenshots/event-1102-log-cleared.png)

![Wazuh Threat Hunting — no results for Event ID 1102](../screenshots/wazuh-1102-no-results.png)

---

*MITRE ATT&CK: https://attack.mitre.org/techniques/T1070/001/*
*Report prepared as part of the SOC Detection Lab portfolio project. All activity was performed in a private, isolated, locally hosted lab environment.*
