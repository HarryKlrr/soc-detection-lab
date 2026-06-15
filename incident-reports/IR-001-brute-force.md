# Incident Report — Brute Force Login Attempt

**Incident ID:** IR-001-2026
**Date Created:** 2026-06-08
**Analyst:** Harry
**Severity:** Medium
**Status:** Closed

---

## 1. Executive Summary

On 8 June 2026, Wazuh picked up 21 failed login attempts against the local account `labvictim` on `Windows10-Target` (192.168.56.20), all within about 10 seconds. That volume and speed is a clear brute force pattern — not a user mistyping their password. I confirmed no successful logon followed the failures, disabled the account as containment, and set an account lockout policy to stop this happening again.

---

## 2. Incident Overview

| Field | Detail |
|---|---|
| **Incident Type** | Credential Brute Force Attack |
| **MITRE Technique** | T1110.001 — Brute Force: Password Guessing |
| **Affected Host** | Windows10-Target — 192.168.56.20 |
| **Affected Account** | `labvictim` (local account) |
| **Detection Time** | 2026-06-08 17:26 UTC |
| **Environment** | Isolated home lab — host-only network 192.168.56.0/24 |

---

## 3. Detection Source

**Platform:** Wazuh SIEM
**Rule triggered:** Built-in `authentication_failed` rule group — Rule ID 60122 (level 5), Rule ID 60204 (level 10 — Multiple Windows Logon Failures)
**Initial indicator:** Spike of 21 Event ID 4625 (Failed Logon) entries for `labvictim` within 10 seconds

---

## 4. Timeline of Events

| Timestamp | Event | Log Source | Notes |
|---|---|---|---|
| 2026-06-08 17:26:46 | First failed logon attempt for `labvictim` | Windows Security Log — Event ID 4625 | Logon Type 2 (Interactive) |
| 2026-06-08 17:26:46 – 17:26:51 | 20 further failed logon attempts | Windows Security Log — Event ID 4625 | 21 total over ~10 seconds |
| 2026-06-08 17:26:48 | Wazuh rule 60204 fired — "Multiple Windows Logon Failures" | Wazuh Threat Hunting | Level 10 alert |
| 2026-06-08 17:26:51 | No Event ID 4624 (Successful Logon) observed | Windows Security Log | Attack did not succeed |
| 2026-06-08 17:27:00 | Account `labvictim` disabled | Windows — net user command | Containment action |
| 2026-06-08 17:30:00 | Account lockout threshold set to 5 failed attempts | Windows — net accounts | Remediation applied |

---

## 5. Indicators Observed

| Indicator Type | Value | Notes |
|---|---|---|
| Source | localhost (192.168.56.20) | Attack originated from same machine — simulated via PowerShell |
| Target Host | Windows10-Target — 192.168.56.20 | Local Windows 10 endpoint |
| Target Account | `labvictim` | Local account created for this scenario |
| Event IDs | 4625 (x21), 4624 (x0) | 21 failures, zero successes |
| Wazuh Rule IDs | 60122 (level 5), 60204 (level 10) | Built-in authentication_failed group |

---

## 6. Investigation Notes

**Step 1 — Alert triage**
Wazuh Threat Hunting showed a burst of 21 Event ID 4625s against `labvictim` in about 10 seconds. 21 failures that fast isn't a user getting their password wrong — it's a script. Rule 60204 fired at level 10 which is what drew my attention.

**Step 2 — Confirmed target account**
Expanded the events in Wazuh and checked the fields:
- `data.win.eventdata.targetUserName` = `labvictim`
- `data.win.system.eventID` = `4625`
- `rule.description` = Logon Failure

**Step 3 — Checked for successful logon**
Filtered for Event ID 4624 for `labvictim` in the same window. Nothing came back — the attack didn't get in.

**Step 4 — Cross-referenced with Windows Event Viewer**
Opened Event Viewer → Windows Logs → Security → filtered for 4625. Same 21 events, which confirms Wazuh is correctly forwarding the Security log.

**Conclusion:** True positive. 21 automated failed logons against a local account, no successful compromise. The source was localhost in this simulation — on a production host the source IP would need to be blocked immediately.

---

## 7. Containment Actions

- Disabled account `labvictim` using `net user labvictim /active:no`
- Verified no Event ID 4624 (successful logon) occurred after the failure cluster
- Applied account lockout policy: threshold set to 5 failed attempts via `net accounts /lockoutthreshold:5`

---

## 8. Remediation Recommendations

- Enforce account lockout across all systems — 5 failed attempts, 30-minute lockout
- Enable MFA on all remote access (RDP, VPN, SSH)
- Remove unused local accounts — `labvictim` had no legitimate purpose
- Consider a honeypot account: any login attempt against it is automatically suspicious

---

## 9. Lessons Learned

- Wazuh's built-in rules caught this with zero custom config — the `authentication_failed` group fired straight away
- Checking for Event ID 4624 after a 4625 cluster is the most important step — it's the difference between a failed attempt and a full compromise
- Volume and speed is what separates brute force from a user making mistakes — 21 failures in 10 seconds is unambiguous
- No lockout policy was in place before this — that's now been fixed

---

## 10. Evidence

| # | Evidence Item | Source |
|---|---|---|
| 1 | `labvictim-account-created.png` | Windows CMD — net user output |
| 2 | `wazuh-brute-force-alert.png` | Wazuh Threat Hunting dashboard |
| 3 | `wazuh-4625-events-filtered.png` | Wazuh — 21 x Event ID 4625 filtered view |
| 4 | `wazuh-4625-event-detail.png` | Wazuh — expanded event detail |
| 5 | `windows-event-viewer-4625.png` | Windows Event Viewer — Security log |
| 6 | `account-lockout-policy.png` | Windows CMD — net accounts output |

---

*MITRE ATT&CK: https://attack.mitre.org/techniques/T1110/001/*
*Report prepared as part of the SOC Detection Lab portfolio project. All activity was performed in a private, isolated, locally hosted lab environment.*
