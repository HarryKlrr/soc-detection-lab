# Playbook — Compromised or Unauthorised Account Activity

**Version:** 1.0
**Last Updated:** 23 June 2026
**Author:** Harry (GitHub: [harryklrr](https://github.com/harryklrr))
**Applies To:** Windows

---

## 1. Purpose

This playbook provides a structured process for investigating and responding to alerts related to **compromised or unauthorised account activity**. It is designed for use by a Tier 1 or Tier 2 SOC analyst.

---

## 2. Alert Trigger

**Alert Name / Rule:** Wazuh Rule 60154 — "Administrators Group Changed" (level 12)

**Trigger Condition:** Evidence of account misuse — e.g., logon from unexpected IP, new account created, account added to privileged group, account used at unusual time.

**Default Severity:** High

---

## 3. Initial Triage

Upon receiving the alert, complete the following steps before escalating or taking action:

- [ ] Identify the affected account and the nature of the suspicious activity
- [ ] Review recent logon history for the account (Event ID 4624)
- [ ] Check for any account management events associated with the account (4720, 4728, 4732)
- [ ] Determine whether the account holder is aware of or initiated the activity
- [ ] Review the source IP for any other suspicious activity

**Initial Questions to Answer:**

- Is the source IP internal or external?
- Is the targeted account a standard user or a privileged account?
- Has this alert fired before for the same source/host? (Check alert history)
- Is there a known change management ticket or approved activity that explains this?

---

## 4. Containment

If the activity is confirmed as suspicious and not authorised:

- [ ] Disable the affected account immediately pending investigation
- [ ] Remove the account from any privileged groups it was added to without authorisation
- [ ] Revoke any active sessions for the account
- [ ] Block the source IP if external or unexpected

> Document all containment actions taken, including timestamps and the analyst performing them.

---

## 5. Remediation

Once the immediate threat is contained:

- [ ] Reset the account password with a strong, unique credential
- [ ] Re-enable the account only after confirming the compromise vector is resolved
- [ ] Review all actions taken under the account during the suspected compromise window
- [ ] Audit all other accounts for similar unauthorised changes

---

## 6. Evidence Collection

Collect and preserve the following evidence before taking any destructive actions:

- [ ] Export relevant SIEM alerts (Wazuh)
- [ ] Export relevant Windows Event Logs or Linux auth logs
- [ ] Screenshot all key alert and investigation views
- [ ] Document the timeline of events

---

## 7. Escalation Criteria

Escalate to a senior analyst or incident response team if:

- [ ] The account is a service account, administrator, or other privileged identity
- [ ] Evidence suggests lateral movement from the compromised account
- [ ] Multiple accounts appear affected
- [ ] Data access or exfiltration is suspected

---

## 8. Post-Incident Actions

- [ ] Complete the incident report using the template in `templates/incident-report-template.md`
- [ ] Update detection rules if gaps were identified
- [ ] Conduct a brief lessons learned review
- [ ] Close the alert in the SIEM with a documented disposition

---

## 9. References

- Relevant MITRE ATT&CK page: https://attack.mitre.org/techniques/T1136/001/
- Relevant detection rule: [`../detection-rules/wazuh-rules.md`](../detection-rules/wazuh-rules.md) — Rule 60154
- Related incident report: [`../incident-reports/IR-003-new-admin-account.md`](../incident-reports/IR-003-new-admin-account.md)

---

*This playbook was developed as part of the SOC Detection Lab portfolio project.*
