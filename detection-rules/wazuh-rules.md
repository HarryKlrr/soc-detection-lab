# Wazuh Detection Rules

Custom rules developed for this lab. All rules are added to the Wazuh Manager in `/var/ossec/etc/rules/local_rules.xml`.

> Rules below are templates. Replace `[Add tested Wazuh rule here]` with your actual tested rule XML once validated in the lab.

---

## Rule: Nmap Port Scan Detection

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Rule to detect multiple connection refused/reset events from a single source in a short window -->
<!-- Relevant log source: Wazuh network/firewall decoder -->
<!-- Example fields to match: srcip, multiple attempts, short time window -->
```

[Insert screenshot of triggered Wazuh alert for Nmap scan here]

---

## Rule: Brute Force — Failed Logins (Windows)

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Threshold rule matching Windows Event ID 4625 — fire after N failures within time window -->
<!-- Example: frequency rule, count > 5 in 60 seconds, same source -->
```

[Insert screenshot of triggered Wazuh alert for failed logins here]

---

## Rule: Brute Force — Failed SSH Logins (Linux)

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Rule matching sshd "Failed password" messages from /var/log/auth.log -->
<!-- Wazuh built-in rules 5710–5712 may already cover this — document which built-in fired or add a custom threshold -->
```

[Insert screenshot of triggered Wazuh alert for failed SSH logins here]

---

## Rule: Suspicious PowerShell Execution

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Rule matching Sysmon Event ID 1 where CommandLine contains -EncodedCommand, -Bypass, or -Hidden -->
<!-- Requires Sysmon integration with Wazuh agent -->
```

[Insert screenshot of triggered Wazuh alert for suspicious PowerShell here]

---

## Rule: New Local Administrator Account Created

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Rule matching Windows Event ID 4720 (account created) and/or 4732 (added to Administrators group) -->
```

[Insert screenshot of triggered Wazuh alert for account creation here]

---

## Rule: Windows Defender Configuration Changed

```xml
<!-- [Add tested Wazuh rule here] -->
<!-- Expected: Rule matching Windows Event ID 5007 where new value indicates a protection feature is disabled -->
```

[Insert screenshot of triggered Wazuh alert for Defender change here]

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

[Add any additional notes from your lab experience here.]
