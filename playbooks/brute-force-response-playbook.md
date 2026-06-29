# Playbook — Brute Force / Failed Login Attempt

**Version:** 1.0
**Last Updated:** 23 June 2026
**Author:** Harry (GitHub: [harryklrr](https://github.com/harryklrr))
**Applies To:** Windows (Linux steps included for completeness — no Linux host was built in this lab)

---

## 1. Purpose

This playbook provides a structured process for investigating and responding to alerts related to **brute force / failed login attempt**. It is designed for use by a Tier 1 or Tier 2 SOC analyst.

---

## 2. Alert Trigger

**Alert Name / Rule:** Wazuh Rule 60204 — "Multiple Windows Logon Failures" (level 10), built-in `authentication_failed` rule group

**Trigger Condition:** Threshold exceeded: N or more failed authentication events (Event ID 4625) from a single source within a defined time window.

**Default Severity:** Medium (escalate to High if successful logon follows)

---

## 3. Initial Triage

Upon receiving the alert, complete the following steps before escalating or taking action:

- [ ] Identify the source IP and targeted account from the alert
- [ ] Determine the number of failed attempts and the time window
- [ ] Check whether any successful logon (Event ID 4624) followed the failures
- [ ] Verify whether the source IP is internal or external to the network
- [ ] Check whether the targeted account has elevated privileges

**Initial Questions to Answer:**

- Is the source IP internal or external?
- Is the targeted account a standard user or a privileged account?
- Has this alert fired before for the same source/host? (Check alert history)
- Is there a known change management ticket or approved activity that explains this?

---

## 4. Containment

If the activity is confirmed as suspicious and not authorised:

- [ ] If confirmed malicious: block the source IP at the firewall
- [ ] If successful logon is found: immediately disable the compromised account
- [ ] If RDP is exposed: consider disabling RDP temporarily or restricting access by IP

> Document all containment actions taken, including timestamps and the analyst performing them.

---

## 5. Remediation

Once the immediate threat is contained:

- [ ] Enforce an account lockout policy (e.g., lock after 5 failed attempts)
- [ ] Implement or enforce MFA for remote access services
- [ ] Review firewall rules to restrict exposure of authentication services
- [ ] Reset the targeted account password if any successful logon was detected

---

## 6. Evidence Collection

Collect and preserve the following evidence before taking any destructive actions:

- [ ] Export relevant SIEM alerts (Wazuh)
- [ ] Export relevant Windows Event Logs
- [ ] Screenshot all key alert and investigation views
- [ ] Document the timeline of events

---

## 7. Escalation Criteria

Escalate to a senior analyst or incident response team if:

- [ ] A successful logon was detected following the failed attempts
- [ ] The targeted account is a privileged or service account
- [ ] Post-compromise activity is observed (process creation, account changes, data access)
- [ ] The attack is ongoing and blocking has not stopped the attempts

---

## 8. Post-Incident Actions

- [ ] Complete the incident report
- [ ] Update detection rules if gaps were identified
- [ ] Conduct a brief lessons learned review
- [ ] Close the alert in the SIEM with a documented disposition

---

## 9. References

- Relevant MITRE ATT&CK page: https://attack.mitre.org/techniques/T1110/001/
- Relevant detection rule: [`../detection-rules/wazuh-rules.md`](../detection-rules/wazuh-rules.md) — Rule 60204
- Related incident report: [`../incident-reports/IR-001-brute-force.md`](../incident-reports/IR-001-brute-force.md)

---

*This playbook was developed as part of the SOC Detection Lab portfolio project.*
