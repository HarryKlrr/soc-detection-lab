# Incident Report — Unauthorised Local Administrator Account Created

**Incident ID:** IR-003-2026
**Date Created:** 2026-06-15
**Analyst:** Harry
**Severity:** High
**Status:** Closed

---

## 1. Executive Summary

On 15 June 2026, Wazuh alerted on a new local account (`labadmin`) being created on `Windows10-Target` (192.168.56.20) and added to the Administrators group within seconds. The speed of that sequence — create account, immediately escalate — is a textbook persistence move. An attacker who's already in and wants a backdoor does exactly this. I confirmed the account hadn't been used yet (no Event ID 4624), disabled it, and removed it. Confirmed as a controlled lab simulation.

---

## 2. Incident Overview

| Field | Detail |
|---|---|
| **Incident Type** | Unauthorised Account Creation / Privilege Escalation |
| **MITRE Technique** | T1136.001 — Create Account: Local Account |
| **Affected Host** | Windows10-Target — 192.168.56.20 |
| **New Account Created** | `labadmin` |
| **Actor Account** | `harry` (administrator) |
| **Detection Time** | 2026-06-15 16:07 UTC |
| **Environment** | Isolated home lab — host-only network 192.168.56.0/24 |

---

## 3. Detection Source

**Platform:** Wazuh SIEM
**Rule triggered:** Rule ID 60154 — Level 12 (High)
**Rule description:** "Administrators Group Changed"
**Initial indicator:** Windows Event ID 4732 — account added to local Administrators group, two hits within seconds of each other

---

## 4. Timeline of Events

| Timestamp | Event | Log Source | Notes |
|---|---|---|---|
| 2026-06-15 16:07:20 | New local account `labadmin` created | Windows Security Log — Event ID 4720 | Created by account `harry` |
| 2026-06-15 16:07:20 | Account `labadmin` added to local Administrators group | Windows Security Log — Event ID 4732 | Immediate privilege escalation |
| 2026-06-15 16:07:20 | Wazuh rule 60154 fired — "Administrators Group Changed" — level 12 | Wazuh Threat Hunting | Two hits — one per group change event |
| 2026-06-15 16:12:25 | Second group change event detected | Wazuh — Rule 60154 | Duplicate from second command run |
| 2026-06-15 16:15:00 | Account `labadmin` disabled and removed | Response action | Containment complete |

---

## 5. Indicators Observed

| Indicator Type | Value | Notes |
|---|---|---|
| Affected Host | Windows10-Target — 192.168.56.20 | Local Windows 10 endpoint |
| New Account | `labadmin` | Created with Password123! — immediately made admin |
| Actor | `harry` | Account that performed the action — subject in event log |
| Event IDs | 4720, 4732 | Account creation + admin group membership change |
| Wazuh Rule | 60154 — Level 12 | Built-in rule for admin group changes |

---

## 6. Investigation Notes

**Step 1 — Alert triage**
Wazuh hit rule 60154 at level 12 twice within seconds — both for the Administrators group being modified. Two hits that close together means two commands ran back to back, which immediately looked like a scripted action rather than someone clicking around in settings.

**Step 2 — Confirmed account details**
Expanded the event in Wazuh and checked the message fields:
- Subject Account Name: `harry` (the account that made the change)
- Target Account Name: `labadmin` (the account being added)
- Target Domain: `WINDOWS10-TARGET`

**Step 3 — Checked for 4720 (account creation)**
Searched for Event ID 4720 to confirm `labadmin` was freshly created rather than a pre-existing account being promoted. Confirmed — the account was created moments before being added to Administrators.

**Step 4 — Checked for logon activity**
Searched Event ID 4624 (successful logon) for `labadmin` after creation. No results — the account was set up but hadn't been used yet. That's consistent with early-stage persistence: the backdoor exists but the attacker hasn't needed it yet.

**Step 5 — Assessed intent**
Creating an account and immediately adding it to Administrators is one of the most common post-exploitation persistence techniques. The assumption is the attacker already has elevated access and is securing a second way back in. This would be a P1 on a live host.

**Conclusion:** True positive. Caught before the account was used. Disabled and removed as containment. The 4720 + 4732 combination in quick succession is what makes this high confidence — it's not ambiguous.

---

## 7. Containment Actions

- Account `labadmin` disabled: `net user labadmin /active:no`
- Account `labadmin` removed from Administrators group
- Account deleted after investigation completed
- Verified no Event ID 4624 (logon) had occurred for `labadmin`

---

## 8. Remediation Recommendations

- Alert on Event ID 4720 (new account) followed by Event ID 4732 (group change) within 60 seconds — that pairing is high-confidence malicious
- No local admin accounts should be created outside a documented change process
- Audit local Administrators group membership on all endpoints regularly
- Enable Windows LAPS to manage local admin credentials centrally

---

## 9. Lessons Learned

- The 4720 + 4732 pairing is a reliable indicator of persistence activity — should always generate a high-severity alert
- Wazuh rule 60154 caught the group change but didn't automatically correlate it with the account creation — connecting those two events is the analyst's job
- The subject field tells you *who did it* — that account may itself be compromised and needs investigating
- Checking Event ID 4624 after account creation is the key question: has the backdoor already been used?

---

## 10. Evidence

| # | Evidence Item | Source |
|---|---|---|
| 1 | `wazuh-admin-group-changed.png` | Wazuh Threat Hunting — rule 60154, 2 hits at level 12 |
| 2 | `wazuh-new-account-detail.png` | Wazuh — expanded event showing target account `labadmin` and subject `harry` |

---

*MITRE ATT&CK: https://attack.mitre.org/techniques/T1136/001/*
*Report prepared as part of the SOC Detection Lab portfolio project. All activity was performed in a private, isolated, locally hosted lab environment.*
