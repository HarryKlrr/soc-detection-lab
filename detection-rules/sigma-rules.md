# Sigma Detection Rules

Sigma is a generic, open-source signature format for SIEM systems. Rules written in Sigma can be converted to Splunk SPL, Wazuh, Elastic, and other query languages using the `sigmac` or `pySigma` converter.

Reference: [https://github.com/SigmaHQ/sigma](https://github.com/SigmaHQ/sigma)

> Rules below are templates. Replace placeholder fields with validated logic once tested in the lab.

---

## Rule: Suspicious PowerShell Execution

```yaml
# [Add tested Sigma rule here]
title: Suspicious PowerShell Execution
status: experimental
description: Detects PowerShell launched with encoded commands or execution policy bypass flags
references:
  - https://attack.mitre.org/techniques/T1059/001/
tags:
  - attack.execution
  - attack.t1059.001
logsource:
  category: process_creation
  product: windows
detection:
  selection:
    Image|endswith: '\powershell.exe'
    CommandLine|contains:
      - '-EncodedCommand'
      - '-Bypass'
      - '-WindowStyle Hidden'
  condition: selection
falsepositives:
  - Legitimate administrative scripts
level: medium
```

---

## Rule: New Local Administrator Account

```yaml
# [Add tested Sigma rule here]
title: New Local Administrator Account Created
status: experimental
description: Detects creation of a new local user account followed by addition to the Administrators group
references:
  - https://attack.mitre.org/techniques/T1136/001/
tags:
  - attack.persistence
  - attack.t1136.001
logsource:
  product: windows
  service: security
detection:
  account_created:
    EventID: 4720
  added_to_admins:
    EventID: 4732
    GroupName: 'Administrators'
  condition: account_created or added_to_admins
falsepositives:
  - Authorised IT provisioning activity
level: high
```

---

## Rule: Windows Defender Disabled

```yaml
# [Add tested Sigma rule here]
title: Windows Defender Real-Time Protection Disabled
status: experimental
description: Detects changes to Windows Defender configuration indicating real-time protection was disabled
references:
  - https://attack.mitre.org/techniques/T1562/001/
tags:
  - attack.defense_evasion
  - attack.t1562.001
logsource:
  product: windows
  service: windefend
detection:
  selection:
    EventID: 5007
    NewValue|contains: 'DisableRealtimeMonitoring'
  condition: selection
falsepositives:
  - Authorised administrator changes
level: high
```

---

## Converting Sigma Rules

To convert these rules to Splunk SPL or Wazuh format, use pySigma or sigmac:

```bash
# Example: convert to Splunk SPL
sigma convert -t splunk sigma-rule.yml

# Example: convert to Wazuh
sigma convert -t wazuh sigma-rule.yml
```

[Add notes on your conversion experience here once you have tested the tooling.]
