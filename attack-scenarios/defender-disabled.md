# Scenario 5 — Windows Defender Disabled

## 1. Scenario Overview

Disabling or modifying endpoint security tools is a common defence evasion technique. Attackers who gain elevated access to a Windows system may attempt to disable Windows Defender to prevent detection of subsequent malicious activity. Monitoring for changes to Defender configuration is an important detection control.

---

## 2. Objective

- Simulate disabling Windows Defender on the Windows target VM in the local lab
- Capture Windows Event Log entries recording the configuration change
- Configure a detection alert in Wazuh
- Document the investigation

---

## 3. Lab Environment

| Component | Detail |
|---|---|
| Target machine | Windows 10 — 192.168.56.20 (host-only network) |
| Detection platform | Wazuh |
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

| Log Source | Event ID | Description |
|---|---|---|
| Windows Defender Operational Log | 5007 | Microsoft Defender Antivirus Configuration has changed |

Key fields observed in Event Viewer:
- Log Name: `Microsoft-Windows-Windows Defender/Operational`
- Event ID: 5007
- User: SYSTEM
- Computer: Windows10-Target
- Registry key changed: `HKLM\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection\DisableRealtimeMonitoring`
- Timestamp: 15 June 2026

> Note: Event ID 5007 was visible in Windows Event Viewer. The Windows Defender Operational log channel was not forwarded to Wazuh by default — this required adding a localfile entry to the Wazuh agent ossec.conf.

---

## 6. Detection Logic

**Windows Event Viewer** confirmed Event ID 5007 generated correctly in `Microsoft-Windows-Windows Defender/Operational`.

**Wazuh:** The Windows Defender Operational log channel requires a manual addition to the Wazuh agent `ossec.conf`:
```xml
<localfile>
  <location>Microsoft-Windows-Windows Defender/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```
This gap was later fully confirmed and fixed during this lab's investigation — see [`../incident-reports/IR-004-defender-disabled.md`](../incident-reports/IR-004-defender-disabled.md) for the complete root-cause analysis and remediation.

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

| File | Description |
|---|---|
| `defender-disabled-event5007.png` | Event Viewer showing Event ID 5007 — Defender configuration changed |
| `defender-reenabled.png` | Windows Security showing real-time protection re-enabled after testing |

![Event Viewer showing Event ID 5007](../screenshots/defender-disabled-event5007.png)
![Windows Security showing Defender re-enabled](../screenshots/defender-reenabled.png)

---

## 9. MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| **Tactic** | Defense Evasion |
| **Technique** | T1562 — Impair Defenses |
| **Sub-technique** | T1562.001 — Disable or Modify Tools |
| **Reference** | https://attack.mitre.org/techniques/T1562/001/ |

---

## 10. Response / Remediation

- Immediately re-enable Windows Defender if it was disabled without authorisation
- Investigate the account and process that made the change
- Review subsequent activity for signs of malware execution or data access
- Implement tamper protection on Windows Defender to prevent unauthorised changes
- Consider GPO or Intune policies to enforce Defender settings

---

## 11. Lessons Learned

- Event ID 5007 is generated in the Windows Defender Operational log — a separate channel from the main Security log, so it requires explicit configuration in Wazuh to forward
- The event shows the exact registry key changed — this tells you precisely what protection was turned off
- In a real incident, Defender being disabled is almost always a precursor to malware execution — the timeline of events immediately after is critical
- Tamper protection should be enabled in enterprise environments to prevent PowerShell commands from being able to disable Defender at all

---

## 12. Status

✅ Complete — 15 June 2026
