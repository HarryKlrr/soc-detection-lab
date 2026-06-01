# Scenario 2 — Failed Login / Brute Force Detection

## 1. Scenario Overview

An attacker attempts to gain access to a target system by repeatedly trying different passwords against an account. This technique is classified as a brute force attack. From a SOC perspective, detecting a high volume of failed authentication events within a short time window is a key indicator of this activity.

---

## 2. Objective

- Simulate repeated failed login attempts against the Windows or Linux target in the local lab
- Identify the relevant authentication log entries
- Configure a threshold-based alert in Wazuh and/or Splunk
- Document the investigation and response

---

## 3. Lab Environment

| Component | Detail |
|---|---|
| Attack machine | Kali Linux — 192.168.56.10 (host-only network) |
| Target machine | Windows 10 (RDP/SMB) / Ubuntu (SSH) |
| Detection platform | Wazuh, Splunk |
| Network | Isolated host-only adapter — no internet routing |

---

## 4. Simulated Activity

The following activity was simulated **only within the local lab environment**:

- **Windows**: Multiple failed RDP or local login attempts generated manually or via a script against a test account
- **Linux**: Multiple failed SSH login attempts against the Ubuntu target from the Kali VM

> All attempts were directed only at locally hosted virtual machines. No external systems were targeted.

---

## 5. Logs Generated

Expected log sources:

- **Windows**: Event ID 4625 (Failed Logon) — visible in Windows Security Event Log and forwarded to Wazuh/Splunk
- **Linux**: `/var/log/auth.log` — `Failed password` entries for the SSH service

[Add actual log entries collected from your lab here.]

---

## 6. Detection Logic

**Wazuh:**
[Add tested Wazuh rule here — threshold rule for multiple 4625 events or auth.log failures — see `detection-rules/wazuh-rules.md`]

**Splunk SPL:**
[Add SPL query here — count of 4625 events by source IP within time window — see `detection-rules/splunk-queries.md`]

---

## 7. Investigation Steps

1. Identify the account being targeted and the source IP
2. Count the number of failed attempts and the time window
3. Determine whether the account was eventually successfully accessed (Event ID 4624 / successful SSH login)
4. Check whether the source IP appears in other log sources around the same time
5. Review whether the account exists and whether it has elevated privileges
6. Determine if this is an internal or external source

---

## 8. Evidence / Screenshots

[Insert screenshot of Wazuh alert showing the brute force detection here]

[Insert screenshot of Windows Event Viewer showing clustered 4625 events here]

[Insert screenshot of Linux auth.log entries showing failed SSH attempts here]

[Insert screenshot of Splunk search results here]

---

## 9. MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| **Tactic** | Credential Access |
| **Technique** | T1110 — Brute Force |
| **Sub-technique** | T1110.001 — Password Guessing |
| **Reference** | https://attack.mitre.org/techniques/T1110/ |

[Update once confirmed in your lab.]

---

## 10. Response / Remediation

- Lock or disable the targeted account temporarily if compromise is suspected
- Block the source IP if confirmed malicious
- Enforce account lockout policies (e.g., lock after 5 failed attempts)
- Enable MFA for remote access services such as RDP and SSH
- Review whether any successful logon followed the failed attempts

---

## 11. Lessons Learned

[Add your reflections here after completing this scenario.]

---

## 12. Status

🔄 In Progress — [Add completion date once fully documented]
