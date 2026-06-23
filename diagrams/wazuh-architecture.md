# Wazuh Architecture (This Lab)

```mermaid
flowchart LR
    subgraph WIN["Windows10-Target — 192.168.56.20"]
        SEC["Security Log"]
        SYS["Sysmon Operational"]
        DEF["Defender Operational<br/>(added to ossec.conf — IR-004)"]
        TASK["TaskScheduler Operational<br/>(enabled — IR-007)"]
        AGENT["Wazuh Agent"]
        SEC --> AGENT
        SYS --> AGENT
        DEF --> AGENT
        TASK --> AGENT
    end

    subgraph MGR["Wazuh Manager — 192.168.56.101"]
        DECODE["Decoder"]
        RULES["Ruleset<br/>(stock rules + local_rules.xml)"]
        ARCHIVE["archives.json<br/>(logall_json — every decoded event)"]
        ALERTS["alerts.json<br/>(rule matches only)"]
        DECODE --> RULES
        RULES -->|"match"| ALERTS
        DECODE --> ARCHIVE
    end

    DASH["Wazuh Dashboard<br/>Threat Hunting / search"]

    AGENT -->|"encrypted, port 1514"| DECODE
    ALERTS --> DASH
    ARCHIVE -.->|"manual rule.id / raw search —<br/>this is what caught IR-006's false negative"| DASH
```

## Notes specific to this lab

- **`archives.json` (logall_json):** disabled by default, enabled lab-wide during the hardening pass as defence-in-depth. It records every decoded event, not just rule matches — this is the only reason IR-006's missed alert could be retroactively confirmed.
- **Two log channels added mid-lab:** Defender Operational (IR-004) and TaskScheduler Operational (IR-007) are not forwarded by a stock Wazuh Windows agent config — both had to be added to `ossec.conf` manually after the gap was found.
- **Custom rule 100004 (IR-007):** lives in `local_rules.xml` on the manager, targets `schtasks.exe` persistence via Sysmon. Despite matching the decoder, event ID, and command-line pattern independently, it does not fire in this deployment — root cause unresolved (see [`../detection-rules/wazuh-rules.md`](../detection-rules/wazuh-rules.md)).
- The Linux Target (192.168.56.30) was never built, so there is no second agent in this architecture — only one Windows agent reports to the manager.
