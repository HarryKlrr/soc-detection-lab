# Sysmon Configuration

## Overview

Sysmon (System Monitor) is deployed on the Windows target VM to provide enhanced process creation, network connection, and file telemetry beyond what standard Windows Event Logging provides.

## Installation

Downloaded `Sysmon.zip` from: https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

Transferred to Windows VM via VirtualBox shared folder, extracted to Desktop.

Install command (PowerShell as Administrator on Windows VM):
```powershell
cd C:\Users\harry\Desktop\Sysmon
.\Sysmon64.exe -accepteula -i C:\Users\harry\Desktop\sysmonconfig-export.xml
```

## Configuration File

Using the SwiftOnSecurity community config — industry standard baseline that balances coverage with low noise:
https://github.com/SwiftOnSecurity/sysmon-config

Config filename: `sysmonconfig-export.xml`

Verify Sysmon is running and config is loaded:
```powershell
.\Sysmon64.exe -s
```

## Key Event IDs Captured

| Sysmon Event ID | Description |
|---|---|
| 1 | Process creation |
| 3 | Network connection |
| 7 | Image loaded (DLL) |
| 10 | Process access |
| 11 | File created |
| 13 | Registry value set |
| 22 | DNS query |

## Forwarding to Wazuh / Splunk

[Add notes on how Sysmon events are forwarded to your SIEM here — e.g., via Wazuh agent or Splunk Universal Forwarder inputs.conf.]

---

*[Insert screenshot of Sysmon events visible in Event Viewer or your SIEM — see `screenshots/`]*
