# Incident Report — Suspicious PowerShell Execution

**Incident ID:** IR-002-2026
**Date Created:** 2026-06-14
**Analyst:** Harry
**Severity:** High
**Status:** Closed

---

## 1. Executive Summary

On 14 June 2026, Wazuh fired a level 12 alert on `Windows10-Target` (192.168.56.20) — PowerShell spawning a child PowerShell process with a Base64-encoded command. The flags used (`-ExecutionPolicy Bypass`, `-NoProfile`, `-WindowStyle Hidden`, `-EncodedCommand`) are a classic malware loader pattern. I decoded the payload using CyberChef and it came back as `whoami` — benign in this lab, but the detection behaviour is exactly what you'd see from a real dropper. No network connections or files were created after execution.

---

## 2. Incident Overview

| Field | Detail |
|---|---|
| **Incident Type** | Suspicious Script Execution |
| **MITRE Technique** | T1059.001 — Command and Scripting Interpreter: PowerShell |
| **Affected Host** | Windows10-Target — 192.168.56.20 |
| **Affected Account** | `harry` (administrator) |
| **Detection Time** | 2026-06-14 14:39 UTC |
| **Environment** | Isolated home lab — host-only network 192.168.56.0/24 |

---

## 3. Detection Source

**Platform:** Wazuh SIEM (via Sysmon Event ID 1)
**Rule triggered:** Rule ID 92057 — Level 12 (High)
**Rule description:** "Powershell.exe spawned a powershell process which executed a base64 encoded command"
**Initial indicator:** Sysmon Event ID 1 (process creation) capturing full command line with `-EncodedCommand`, `-ExecutionPolicy Bypass`, `-NoProfile`, `-WindowStyle Hidden` flags

---

## 4. Timeline of Events

| Timestamp | Event | Log Source | Notes |
|---|---|---|---|
| 2026-06-14 14:39:42 | PowerShell.exe spawned child PowerShell process with encoded command | Sysmon Event ID 1 | Full command line captured |
| 2026-06-14 14:39:42 | Wazuh rule 92057 fired — level 12 | Wazuh Threat Hunting | High severity alert |
| 2026-06-14 14:39:42 | Child process executed and exited immediately | Sysmon | No persistence observed |
| 2026-06-14 14:40:00 | No Sysmon Event ID 3 (network connection) observed | Sysmon | No outbound connection made |
| 2026-06-14 14:40:00 | No Sysmon Event ID 11 (file created) observed | Sysmon | No files dropped |

---

## 5. Indicators Observed

| Indicator Type | Value | Notes |
|---|---|---|
| Affected Host | Windows10-Target — 192.168.56.20 | Local Windows 10 endpoint |
| Process | `powershell.exe` | Child process spawned by parent PowerShell |
| Parent Process | `powershell.exe` | PowerShell spawning PowerShell is suspicious |
| Command Line Flags | `-ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -EncodedCommand` | Classic malware loader pattern |
| Encoded Command | `dwBoAG8AYQBtAGkA` | Decodes to `whoami` — reconnaissance command |
| Wazuh Rule | 92057 — Level 12 | Built-in high-severity rule |
| Sysmon Event | Event ID 1 | Process creation with full command line |

---

## 6. Investigation Notes

**Step 1 — Alert triage**
Rule 92057 at level 12 is the highest alert I saw in this lab. The description said it all: PowerShell spawning a child PowerShell with a Base64 command. This is what malware loaders, RATs, and post-exploitation frameworks look like — the encoding is there to hide the payload from basic string matching.

**Step 2 — Examined command line**
Pulled `data.win.eventdata.commandLine` from the expanded event:
```
powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -EncodedCommand dwBoAG8AYQBtAGkA
```
Breaking down the flags:
- `-ExecutionPolicy Bypass` — ignores script execution policy, lets unsigned scripts run
- `-NoProfile` — skips the PowerShell profile, fewer forensic artefacts left behind
- `-WindowStyle Hidden` — no visible window, user wouldn't notice anything
- `-EncodedCommand` — the payload is Base64, obfuscated from basic detection

**Step 3 — Decoded the Base64 payload**
Put `dwBoAG8AYQBtAGkA` into CyberChef → decoded to `whoami`. Benign here, but the same technique is used to hide download cradles (`IEX (New-Object Net.WebClient).DownloadString(...)`) or credential dumping commands in real attacks.

**Step 4 — Reviewed parent process**
`data.win.eventdata.parentImage` = `powershell.exe`. PowerShell spawning itself is a red flag on its own. In an actual attack the parent is usually something like `winword.exe` (malicious macro) or `mshta.exe` — that's what tells you how the attacker got in.

**Step 5 — Checked for follow-on activity**
Searched Sysmon Event ID 3 (network connections) and Event ID 11 (file creation) in the same window — nothing. Process ran and exited clean.

**Conclusion:** True positive. The command line flags alone are enough to escalate this — the encoded payload just confirms it. Payload was benign in the lab. On a live endpoint this would mean isolating the host and checking memory.

---

## 7. Containment Actions

- Verified no network connections were made by the PowerShell process
- Verified no files were dropped
- Confirmed no persistence mechanisms were created
- On a live endpoint: isolate the host, preserve memory, escalate to Tier 2

---

## 8. Remediation Recommendations

- Enable PowerShell Script Block Logging (Event ID 4104) — this logs the *decoded* content of encoded commands, so Base64 obfuscation becomes useless
- Enable Module Logging and Transcription for a full audit trail
- Alert on any process spawning PowerShell with `-EncodedCommand` or `-WindowStyle Hidden`
- The parent process matters — trace back to understand the initial access vector

---

## 9. Lessons Learned

- Without Sysmon, Wazuh only knows PowerShell ran — not *what* it ran. Sysmon Event ID 1 with the full command line is what makes this investigation possible
- Sysmon doesn't forward to Wazuh automatically after install — you have to add the channel to `ossec.conf` manually. Caught this during setup
- Base64 decoding is a basic analyst skill — CyberChef handles it in seconds
- Rule 92057 fired with no custom tuning needed, which shows how much Wazuh's default ruleset covers PowerShell abuse out of the box

---

## 10. Evidence

| # | Evidence Item | Source |
|---|---|---|
| 1 | `wazuh-powershell-detected.png` | Wazuh Threat Hunting — rule 92057 fired, level 12 |
| 2 | `wazuh-powershell-cmdline.png` | Wazuh — expanded event showing full commandLine and parentImage |

---

*MITRE ATT&CK: https://attack.mitre.org/techniques/T1059/001/*
*Report prepared as part of the SOC Detection Lab portfolio project. All activity was performed in a private, isolated, locally hosted lab environment.*
