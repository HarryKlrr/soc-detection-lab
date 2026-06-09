# Wazuh Setup

## Overview

Wazuh is deployed as the primary HIDS and SIEM platform using the official pre-built OVA appliance imported into VirtualBox.

| Component | Detail |
|---|---|
| Deployment method | Pre-built OVA (Wazuh all-in-one appliance) |
| Manager IP | 192.168.56.101 (static — set via `nmtui` on the Wazuh VM) |
| Dashboard URL | https://192.168.56.101 |
| Network | Host-only — isolated from internet |

## Installation

### Wazuh Manager (OVA Method)

1. Download the Wazuh OVA from: https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html
2. In VirtualBox: File → Import Appliance → select the `.ova` file
3. Once imported, start the VM
4. Log into the VM console and set a static IP using `sudo nmtui`
5. Set IP to `192.168.56.101`, subnet `255.255.255.0`
6. Access the dashboard at `https://192.168.56.101` from the host machine

### Wazuh Agent — Windows Target

Transferred the Wazuh agent MSI to the Windows VM via a VirtualBox shared folder (requires Guest Additions installed on the Windows VM).

Install command (PowerShell as Administrator on Windows VM):
```powershell
msiexec.exe /i wazuh-agent.msi /q WAZUH_MANAGER="192.168.56.101" WAZUH_AGENT_NAME="Windows10-Target"
```

Start the agent:
```cmd
NET START WazuhSvc
```

## Verification

Confirmed working:
- Agent status: **Active** — visible in Wazuh dashboard under Agents
- Events flowing into Threat Hunting — Event ID 4625 alerts detected during brute force scenario
