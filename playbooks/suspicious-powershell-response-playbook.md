# Playbook — Suspicious PowerShell Execution

**Version:** 1.0
**Last Updated:** 23 June 2026
**Author:** Harry (GitHub: [harryklrr](https://github.com/harryklrr))
**Applies To:** Windows

---

## 1. Purpose

This playbook provides a structured process for investigating and responding to alerts related to **suspicious powershell execution**. It is designed for use by a Tier 1 or Tier 2 SOC analyst.

---

## 2. Alert Trigger

**Alert Name / Rule:** Wazuh Rule 92057 — "Powershell.exe spawned a powershell process which executed a base64 encoded command" (level 12)

**Trigger Condition:** Detection of PowerShell process creation with encoded commands, execution policy bypass, hidden window, or unusual parent process.

**Default Severity:** Medium to High (depends on parent process and command content)

---

## 3. Initial Triage

Upon receiving the alert, complete the following steps before escalating or taking action:

- [ ] Identify the host, user, and full command line from the process creation event
- [ ] Note the parent process — is it an expected launcher for PowerShell in this environment?
- [ ] Decode any Base64-encoded command line arguments to understand intent
- [ ] Check for network connections made by the PowerShell process (Sysmon Event ID 3)
- [ ] Check for files created during the session (Sysmon Event ID 11)

**Initial Questions to Answer:**

- Is the source IP internal or external?
- Is the targeted account a standard user or a privileged account?
- Has this alert fired before for the same source/host? (Check alert history)
- Is there a known change management ticket or approved activity that explains this?

---

## 4. Containment

If the activity is confirmed as suspicious and not authorised:

- [ ] If confirmed malicious: isolate the affected host from the network (lab simulation: power off or disconnect VM)
- [ ] Terminate the malicious PowerShell process if still running
- [ ] Preserve memory and process state before termination if possible

> Document all containment actions taken, including timestamps and the analyst performing them.

---

## 5. Remediation

Once the immediate threat is contained:

- [ ] Review and restrict PowerShell execution via Constrained Language Mode or AppLocker
- [ ] Enable PowerShell Script Block Logging (Event ID 4104) if not already active
- [ ] Check for and remove any persistence mechanisms created during the session
- [ ] Reset credentials for any accounts used during the session

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

- [ ] The PowerShell session made outbound network connections
- [ ] Files were created in temp or unusual directories
- [ ] Encoded commands decode to reveal clearly malicious intent (e.g., download and execute)
- [ ] The host shows further signs of compromise after the PowerShell activity

---

## 8. Post-Incident Actions

- [ ] Complete the incident report using the template in `templates/incident-report-template.md`
- [ ] Update detection rules if gaps were identified
- [ ] Conduct a brief lessons learned review
- [ ] Close the alert in the SIEM with a documented disposition

---

## 9. References

- Relevant MITRE ATT&CK page: https://attack.mitre.org/techniques/T1059/001/
- Relevant detection rule: [`../detection-rules/wazuh-rules.md`](../detection-rules/wazuh-rules.md) — Rule 92057
- Related incident report: [`../incident-reports/IR-002-suspicious-powershell.md`](../incident-reports/IR-002-suspicious-powershell.md)

---

*This playbook was developed as part of the SOC Detection Lab portfolio project.*
