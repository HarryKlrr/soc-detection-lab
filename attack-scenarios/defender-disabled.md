# Scenario 5 — Windows Defender Disabled

## 1. Scenario Overview

Disabling or modifying endpoint security tools is a common defence evasion technique. Attackers who gain elevated access to a Windows system may attempt to disable Windows Defender to prevent detection of subsequent malicious activity. Monitoring for changes to Defender configuration is an important detection control.

---

## 2. Objective

- Simulate disabling Windows Defender on the Windows target VM in the local lab
- Capture Windows Event Log entries recording the configuration change
- Configure a detection alert in Wazuh and/or Splunk
- Document the investigation

---

## 3. Lab Environment

| Component | Detail |
|---|---|
| Target machine | Windows 10 — 192.168.56.20 (host-only network) |
| Detection platform | Wazuh, Splunk |
| Log source | Windows Event ID 5007 — Defender configuration changed |
| Network | Isolated host-only adapter — no internet routing |

---

## 4. Simulated Activity

The following commands were run **only on the local Windows lab VM** as an administrator to simulate a defence evasion technique:

```powershell
# Disable real-time protection (simulated on local lab VM only)
Set-MpPreference -DisableRealtimeMonitoring $true

# Re-enable after testing
Set-MpPreference -DisableRealtimeMonitoring $false
```

> This was performed only on the isolated lab VM and was immediately reversed after log collection. This technique is documented here purely for detection engineering purposes.

---

## 5. Logs Generated

Expected log sources:

- **Windows Event ID 5007**: Microsoft Antimalware — configuration has changed
- The event will show the old and new values for the modified Defender setting

[Add actual log entries collected from your lab here.]

---

## 6. Detection Logic

**Wazuh:**
[Add tested Wazuh rule here — alert on Event ID 5007 where Defender configuration is disabled — see `detection-rules/wazuh-rules.md`]

**Splunk SPL:**
[Add SPL query here — search for Event ID 5007 with relevant configuration value change — see `detection-rules/splunk-queries.md`]

---

## 7. Investigation Steps

1. Identify the Event ID 5007 log entry — note the exact configuration change
2. Determine the account that made the change (subject in the event log)
3. Identify the process that initiated the change (if captured by Sysmon Event ID 1)
4. Review the timeline — did any process execution or file creation events occur after Defender was disabled?
5. Determine whether the change was authorised (e.g., a legitimate admin action) or suspicious
6. Check for other evasion indicators in the same time window

---

## 8. Evidence / Screenshots

[Insert screenshot of Windows Event ID 5007 here]

[Insert screenshot of Wazuh alert triggered by the Defender configuration change here]

[Insert screenshot of Splunk search results here]

---

## 9. MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| **Tactic** | Defense Evasion |
| **Technique** | T1562 — Impair Defenses |
| **Sub-technique** | T1562.001 — Disable or Modify Tools |
| **Reference** | https://attack.mitre.org/techniques/T1562/001/ |

[Update once confirmed in your lab.]

---

## 10. Response / Remediation

- Immediately re-enable Windows Defender if it was disabled without authorisation
- Investigate the account and process that made the change
- Review subsequent activity for signs of malware execution or data access
- Implement tamper protection on Windows Defender to prevent unauthorised changes
- Consider GPO or Intune policies to enforce Defender settings

---

## 11. Lessons Learned

[Add your reflections here after completing this scenario.]

---

## 12. Status

🔄 In Progress — [Add completion date once fully documented]
