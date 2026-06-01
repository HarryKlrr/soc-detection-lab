# Sysmon Configuration

## Overview

Sysmon (System Monitor) is deployed on the Windows target VM to provide enhanced process creation, network connection, and file telemetry beyond what standard Windows Event Logging provides.

## Installation

Sysmon is available from the Microsoft Sysinternals suite:
[https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

## Configuration File

A community Sysmon configuration is recommended as a baseline. The SwiftOnSecurity config is widely used in SOC environments:
[https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config)

[Add notes on the specific config file used in this lab here.]

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
