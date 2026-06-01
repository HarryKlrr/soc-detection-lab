# Scenario 4 — New Administrator Account Created

## 1. Scenario Overview

Creating a new local administrator account is a common persistence technique used by attackers after gaining access to a system. It provides a backdoor that survives reboots and password changes on existing accounts. Monitoring for account creation events — particularly those adding accounts to privileged groups — is a key detection control.

---

## 2. Objective

- Simulate the creation of a new local administrator account on the Windows target VM
- Capture Windows Event Log entries for account creation and group membership changes
- Configure a detection alert in Wazuh and/or Splunk
- Document the investigation

---

## 3. Lab Environment

| Component | Detail |
|---|---|
| Target machine | Windows 10 — 192.168.56.20 (host-only network) |
| Detection platform | Wazuh, Splunk |
| Log source | Windows Security Event Log — Event IDs 4720, 4728, 4732 |
| Network | Isolated host-only adapter — no internet routing |

---

## 4. Simulated Activity

The following commands were run **only on the local Windows lab VM** to simulate a persistence technique:

```cmd
# Create a new local user account
net user labtest Password123! /add

# Add the account to the local Administrators group
net localgroup administrators labtest /add
```

> Executed only within the isolated lab VM. This simulates a technique an attacker might use after gaining initial access.

---

## 5. Logs Generated

Expected log sources:

- **Windows Event ID 4720**: A user account was created
- **Windows Event ID 4728**: A member was added to a security-enabled global group
- **Windows Event ID 4732**: A member was added to a security-enabled local group (Administrators)

[Add actual log entries collected from your lab here.]

---

## 6. Detection Logic

**Wazuh:**
[Add tested Wazuh rule here — alert on Event ID 4720 and 4732 — see `detection-rules/wazuh-rules.md`]

**Splunk SPL:**
[Add SPL query here — search for Event ID 4720 and 4732 with group name "Administrators" — see `detection-rules/splunk-queries.md`]

---

## 7. Investigation Steps

1. Identify the newly created account name and when it was created
2. Determine which account created the new user (the subject/actor in the event log)
3. Check whether the new account was immediately added to a privileged group
4. Review whether the account has been used to log on (Event ID 4624) since creation
5. Look for the originating process or session — was this done interactively, remotely, or via a script?
6. Check for other persistence indicators around the same time window

---

## 8. Evidence / Screenshots

[Insert screenshot of Windows Event ID 4720 here]

[Insert screenshot of Windows Event ID 4732 showing the Administrators group here]

[Insert screenshot of Wazuh alert triggered by the account creation here]

[Insert screenshot of Splunk search results here]

---

## 9. MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| **Tactic** | Persistence |
| **Technique** | T1136 — Create Account |
| **Sub-technique** | T1136.001 — Local Account |
| **Reference** | https://attack.mitre.org/techniques/T1136/001/ |

[Update once confirmed in your lab.]

---

## 10. Response / Remediation

- Immediately disable and investigate the newly created account
- Determine whether the account was created by an authorised administrator or by a compromised process
- Remove the account from the Administrators group if unauthorised
- Review privileged group membership across all accounts
- Check for additional persistence mechanisms (scheduled tasks, registry run keys, services)

---

## 11. Lessons Learned

[Add your reflections here after completing this scenario.]

---

## 12. Status

🔄 In Progress — [Add completion date once fully documented]
