# Scenario 3 — Suspicious PowerShell Execution

## 1. Scenario Overview

Attackers frequently abuse PowerShell to execute payloads, download files, and perform reconnaissance on Windows systems. Detecting unusual PowerShell execution — particularly with encoded commands, bypassed execution policies, or non-standard parent processes — is a core SOC analyst skill.

---

## 2. Objective

- Simulate suspicious PowerShell activity on the Windows target VM in the local lab
- Capture the relevant Sysmon and Windows Event Log entries
- Create detection logic in Wazuh and/or Splunk to identify the activity
- Document the investigation

---

## 3. Lab Environment

| Component | Detail |
|---|---|
| Target machine | Windows 10 — 192.168.56.20 (host-only network) |
| Detection platform | Wazuh (Sysmon integration), Splunk |
| Log source | Sysmon Event ID 1 (process creation), Windows Event ID 4688 |
| Network | Isolated host-only adapter — no internet routing |

---

## 4. Simulated Activity

The following commands were run **only on the local Windows lab VM** to simulate suspicious PowerShell usage:

```powershell
# Example: Encoded command (simulates obfuscation)
powershell.exe -EncodedCommand [Base64EncodedString]

# Example: Execution policy bypass (no malicious payload — for detection testing only)
powershell.exe -ExecutionPolicy Bypass -NoProfile -Command "Get-Process"

# Example: Downloading a file (to a local test URL only — no external connections)
powershell.exe -Command "Invoke-WebRequest -Uri http://192.168.56.10/test.txt -OutFile C:\Temp\test.txt"
```

> All commands were executed locally within the isolated lab. No external URLs were contacted.

---

## 5. Logs Generated

Expected log sources:

- **Sysmon Event ID 1**: Process creation — captures full command line including encoded parameters
- **Windows Event ID 4688**: Process creation (if audit policy is enabled)
- **Wazuh alert**: Rule match on suspicious PowerShell flags (EncodedCommand, Bypass, etc.)

[Add actual log entries collected from your lab here.]

---

## 6. Detection Logic

**Wazuh:**
[Add tested Wazuh rule here — detect PowerShell with EncodedCommand, Bypass, or hidden window flags — see `detection-rules/wazuh-rules.md`]

**Splunk SPL:**
[Add SPL query here — search Sysmon Event ID 1 for PowerShell with suspicious arguments — see `detection-rules/splunk-queries.md`]

**Sigma:**
[Add Sigma rule reference here — see `detection-rules/sigma-rules.md`]

---

## 7. Investigation Steps

1. Identify the process creation event — note the full command line arguments
2. Check the parent process — was PowerShell launched by an expected parent (e.g., explorer.exe, a known admin tool) or something unusual?
3. Review whether encoded commands or execution policy bypasses were used
4. Check for any network connections made by the PowerShell process (Sysmon Event ID 3)
5. Review whether any files were created or modified (Sysmon Event ID 11)
6. Determine the user context — was this a standard user or an admin account?
7. Look for additional activity from the same user or host in the surrounding time window

---

## 8. Evidence / Screenshots

[Insert screenshot of Sysmon Event ID 1 showing the PowerShell process creation here]

[Insert screenshot of Wazuh alert triggered by the PowerShell activity here]

[Insert screenshot of Splunk search results here]

---

## 9. MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| **Tactic** | Execution |
| **Technique** | T1059 — Command and Scripting Interpreter |
| **Sub-technique** | T1059.001 — PowerShell |
| **Reference** | https://attack.mitre.org/techniques/T1059/001/ |

[Update once confirmed in your lab.]

---

## 10. Response / Remediation

- Investigate the full scope of the PowerShell session — what was executed and what data was accessed
- Check for persistence mechanisms that may have been created
- Review whether encoded or obfuscated scripts were decoded and executed
- Consider restricting PowerShell execution via Constrained Language Mode or AppLocker
- Ensure PowerShell Script Block Logging is enabled (Event ID 4104)

---

## 11. Lessons Learned

[Add your reflections here after completing this scenario.]

---

## 12. Status

🔄 In Progress — [Add completion date once fully documented]
