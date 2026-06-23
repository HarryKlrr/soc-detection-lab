# SOC Detection Workflow

This isn't a generic textbook flow — it's shaped directly by what actually happened in this lab, including two real detection gaps (IR-004, IR-007) and one search methodology mistake that got corrected (IR-006).

```mermaid
flowchart TD
    A["Log generated on endpoint<br/>(Security log, Sysmon, Defender Operational, etc.)"] --> B{"Log channel forwarded<br/>to Wazuh agent?"}
    B -- "No — e.g. IR-004, IR-007" --> B1["Detection gap:<br/>event never reaches Wazuh at all"]
    B1 --> B2["Fix: add channel to agent ossec.conf<br/>(done in IR-004 and IR-007)"]
    B2 --> A
    B -- "Yes" --> C{"Does a stock or custom<br/>Wazuh rule match the event?"}
    C -- "No matching rule" --> C1["Event decoded but discarded —<br/>no alert generated<br/>(IR-007's unresolved rule 100004)"]
    C -- "Rule matches" --> D["Alert generated in Wazuh"]
    D --> E["Analyst searches / triages the alert"]
    E --> F{"Search comes back<br/>with no results?"}
    F -- "Yes" --> F1["⚠ Verify the search itself before<br/>concluding a gap exists —<br/>check rule.id / archives.json directly"]
    F1 --> F2{"Alert was actually<br/>present all along?"}
    F2 -- "Yes — false negative in the search<br/>(IR-006)" --> G["Correct the record —<br/>retract the 'gap' finding"]
    F2 -- "No — genuine gap" --> B1
    F -- "No, alert found normally" --> H["Full investigation:<br/>timeline, indicators, containment"]
    H --> I["Incident report written<br/>(incident-reports/IR-00X)"]
    I --> J["Remediation recommendations<br/>+ lessons learned"]
```

## Why the verification loop exists

IR-006 documented what looked like a missing Wazuh rule for Event ID 1102 — a free-text dashboard search came back empty. A later re-investigation found the stock rule (63103) had been firing correctly the whole time, including on the original incident date; the search itself was the problem, not the SIEM. That's the `F → F1 → F2` branch above: an empty search result gets the same scrutiny as a firing alert before it's written up as a gap.

## Where this lab's two real gaps sit

- **IR-004** (Defender Operational log never forwarded) and the audit-policy half of **IR-007** (Event 4698/106 never generated at all by default) both sit at the `B1/B2` branch — the fix was at the OS/logging level, not the rule level.
- **IR-007**'s custom Sysmon rule (ID 100004) sits at `C1` — every component was confirmed correct against the live event, but it still doesn't fire, and the root cause is unidentified. This is the one open item this workflow hasn't fully closed yet.
