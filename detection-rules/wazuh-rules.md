# Wazuh Detection Rules

Custom rules developed for this lab. All rules are added to the Wazuh Manager in `/var/ossec/etc/rules/local_rules.xml`.

---

## Rule: Nmap Port Scan Detection

**Status:** ❌ Not applicable. Wazuh is a HIDS and never sees an external port scan — it doesn't generate a Windows Event Log entry, so there's nothing for any Wazuh rule (stock or custom) to match. Closing this would require a NIDS (e.g. Suricata) or Windows Firewall connection logging forwarded to Wazuh, neither of which is deployed in this lab. See [`../attack-scenarios/nmap-scan-detection.md`](../attack-scenarios/nmap-scan-detection.md) and [`../incident-reports/IR-005-nmap-scan.md`](../incident-reports/IR-005-nmap-scan.md).

---

## Rule: Brute Force — Failed Logins (Windows)

**Status:** ✅ Covered by stock rules — confirmed firing on live data, no custom rule required.

Wazuh's built-in `authentication_failed` rule group fired automatically: Rule 60122 (level 5) and Rule 60204 (level 10 — "Multiple Windows Logon Failures") both matched the burst of 21 x Event ID 4625 against `labvictim`. Full writeup: [`../incident-reports/IR-001-brute-force.md`](../incident-reports/IR-001-brute-force.md).

---

## Rule: Brute Force — Failed SSH Logins (Linux)

**Status:** N/A — the Linux target VM was planned for this lab (see `../lab-setup/network-design.md`) but never built, so no SSH auth log data exists to test against. Wazuh's stock rules 5710–5712 are the relevant ones to test if a Linux target is added in a future phase — see [`../investigation-notes/linux-auth-logs.md`](../investigation-notes/linux-auth-logs.md).

---

## Rule: Suspicious PowerShell Execution

**Status:** ✅ Covered by stock rule — confirmed firing on live data, no custom rule required.

Wazuh's built-in Rule 92057 (level 12) fired automatically on the Sysmon Event ID 1 `commandLine` containing `-EncodedCommand`. Full writeup: [`../incident-reports/IR-002-suspicious-powershell.md`](../incident-reports/IR-002-suspicious-powershell.md).

---

## Rule: New Local Administrator Account Created

**Status:** ✅ Covered by stock rule — confirmed firing on live data, no custom rule required.

Wazuh's built-in Rule 60154 (level 12 — "Administrators Group Changed") fired automatically on Event ID 4732. Full writeup: [`../incident-reports/IR-003-new-admin-account.md`](../incident-reports/IR-003-new-admin-account.md).

---

## Rule: Windows Defender Configuration Changed

**Status:** ✅ Covered by stock rule — confirmed firing on live data, no custom rule required.

The Windows Defender Operational log channel wasn't reaching Wazuh at all — Event ID 5007 only ever showed up in local Event Viewer. This was fixed by adding the channel to the agent's `ossec.conf` (see [`../incident-reports/IR-004-defender-disabled.md`](../incident-reports/IR-004-defender-disabled.md)). Retested 2026-06-23: re-disabled real-time protection, confirmed Event ID 5007 logged locally, then checked `alerts.json` directly on the manager (`sudo grep '"eventID":"5007"' /var/ossec/logs/alerts/alerts.json`). Wazuh's stock ruleset already includes Rule **62154** ("Windows Defender: Antimalware platform feature configuration changed," level 5), and it fired correctly — no custom rule needed. This closes the gap.

---

## Rule: Audit Log Cleared (Event ID 1102)

```xml
<!-- No custom rule needed — Wazuh's stock ruleset already covers this. -->
<!-- Rule 63103 (built into Wazuh's ruleset) matches win.system.eventID == 1102, level 5, MITRE T1070. -->
```

**Status:** ✅ Covered by stock rule — confirmed firing on live data, no custom rule required.

Originally documented as a gap in IR-006 after a Wazuh search for Event ID 1102 returned no results. Re-investigation found the search itself was flawed: rule 63103 had alerted correctly, including on the original incident date, confirmed directly via `archives.json` and a `rule.id:63103` search. Full writeup: [`investigation-notes/detection-engineering-writeup.md`](../investigation-notes/detection-engineering-writeup.md).

---

## Rule: Scheduled Task Created via Security Log (Event ID 4698)

```xml
<!-- No custom rule needed once auditing is enabled. -->
<!-- Rule 60228 (built into Wazuh's ruleset) matches win.system.eventID == 4698. -->
<!-- Prerequisite (disabled by default on stock Windows 10): -->
<!-- auditpol /set /subcategory:"Other Object Access Events" /success:enable -->
```

**Status:** ✅ Covered by stock rule, conditional on audit policy — confirmed firing once the prerequisite subcategory was enabled.

IR-007 found that Event ID 4698 never logged at all on a stock Windows 10 install — not a SIEM gap, a missing OS-level audit setting. After enabling the subcategory above, the event logs and Wazuh's stock rule 60228 alerts on it with no custom rule needed. Full writeup: [`investigation-notes/detection-engineering-writeup.md`](../investigation-notes/detection-engineering-writeup.md).

---

## Rule: Scheduled Task Persistence via Sysmon (schtasks.exe + SYSTEM + PowerShell)

**Status:** ⚠️ Attempted, unresolved. Documented as an open finding, not a working detection.

The 4698 path above only fires if that audit subcategory is enabled. Sysmon sees the same `schtasks.exe` process creation regardless of that setting, so this rule was an attempt at a stronger, audit-policy-independent detection of the same technique (T1053.005).

```xml
<group name="windows,sysmon,scheduled_task,">
  <rule id="100004" level="12">
    <decoded_as>windows_eventchannel</decoded_as>
    <field name="win.system.eventID">^1$</field>
    <field name="win.eventdata.commandLine" type="pcre2">(?i)^schtasks\b(?=.*(?:/sc\s+onlogon|/ru\s+system))(?=.*powershell)</field>
    <description>Scheduled task created via schtasks.exe with SYSTEM/logon-trigger persistence and a PowerShell payload</description>
    <mitre>
      <id>T1053.005</id>
    </mitre>
    <group>persistence,scheduled_task,sysmon,</group>
  </rule>
</group>
```

Despite the decoder name, event ID, and command-line value all being independently confirmed correct against the live captured event, this rule never fired. An earlier version chained off Wazuh's stock Sysmon classification rule (`if_sid 184665`); a full audit of `archives.json` found that rule — and every rule in its `18xxx` range — has never fired once on any live event in this lab, meaning the stock Sysmon ruleset itself doesn't match this deployment's JSON-decoded eventchannel format. The version above removes that dependency and matches on the decoder and fields directly, but still doesn't fire live. Root cause not yet identified — investigation paused rather than continued indefinitely. Full diagnostic trail: [`investigation-notes/detection-engineering-writeup.md`](../investigation-notes/detection-engineering-writeup.md).

---

## Notes on Adding Custom Rules

1. Custom rules are added to `/var/ossec/etc/rules/local_rules.xml` on the Wazuh Manager
2. Rule IDs for custom rules must be between `100000` and `120000`
3. After editing, restart the Wazuh Manager: `systemctl restart wazuh-manager`
4. Test rules using `wazuh-logtest` before deploying
