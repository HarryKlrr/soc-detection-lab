# Detection Engineering Writeup — Wazuh Rule Coverage Audit

**Date:** 2026-06-22
**Related Incidents:** IR-006 (Log Clearing), IR-007 (Scheduled Task Persistence)

## Why This Exists

Two incident reports in this lab — IR-006 and IR-007 — originally documented Wazuh detection gaps based on an alert search returning no results. Before accepting either as a real gap, a hardening pass was run to check each one directly against Wazuh's actual rule-matching pipeline rather than its dashboard search, and to write a custom rule for any gap that turned out to be genuine. This is the consolidated record of that work: one false alarm, one real gap that was already solved by existing stock coverage, and one open question that's still unresolved.

---

## Finding 1 — Event ID 1102 (Log Clearing): False Alarm, Not a Gap

IR-006 originally reported no Wazuh coverage for Event ID 1102 ("the audit log was cleared"). Checking Wazuh's stock ruleset directly (`grep -rl "63103" /var/ossec/ruleset/rules/`) found that rule 63103 already matches `win.system.eventID == 1102` (level 5, MITRE T1070). Regenerating the event and checking `archives.json` confirmed it fired immediately — and a broader `rule.id:63103` search with no date filter showed it had also fired correctly on the original incident date. The original "no results" search had queried free text against the dashboard instead of the indexed rule or event-ID field. No custom rule was needed; the gap was in the search methodology, not the SIEM. IR-006 has been corrected accordingly.

## Finding 2 — Event ID 4698 (Scheduled Task Created, Security Log): Real Gap, Already Closed by Stock Rule

IR-007 originally reported that Event ID 4698 ("a scheduled task was created") never logged at all. Root cause: the "Other Object Access Events" audit subcategory is disabled by default on Windows 10, so the event is never generated in the first place — not a forwarding problem, not a missing rule. After enabling it (`auditpol /set /subcategory:"Other Object Access Events" /success:enable`), 4698 began logging, and Wazuh's stock rule 60228 picked it up correctly with no custom rule required. This path is now fully closed.

## Finding 3 — Sysmon-Based Detection of the Same Technique: Unresolved

Coverage via 4698 depends entirely on that audit subcategory being enabled — something an attacker, or just a default install, could leave off. Sysmon records the same `schtasks.exe` process creation regardless of that setting, so a Sysmon-based rule would be a more resilient, audit-policy-independent detection for the same technique (T1053.005 — Scheduled Task/Job). This is where the investigation hit a wall.

### What was built

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

Matches Sysmon Event ID 1 where the command line starts with `schtasks`, contains `/sc onlogon` or `/ru SYSTEM`, and contains `powershell` — the exact combination used to create a hidden, SYSTEM-level, logon-triggered scheduled task.

### What was verified correct, individually

- The event reaches Wazuh and decodes correctly — `archives.json` shows `"decoder":{"name":"windows_eventchannel"}` for the captured event.
- `win.system.eventID` is a flat string field (`"1"`), not a nested object — ruled out a known Wazuh JSON-nesting gotcha as the cause.
- `win.eventdata.commandLine` contains the exact expected value (`schtasks /create /tn "WindowsUpdateCheck" /tr "powershell.exe -WindowStyle Hidden -Command Write-Host pwned" /sc onlogon /ru SYSTEM`), which satisfies every condition of the regex by direct inspection.

### What didn't work, and why

The first version of the rule chained off Wazuh's stock Sysmon classification rule (`if_sid 184665`, defined in `0330-sysmon_rules.xml`). It never fired. Testing through `wazuh-logtest` was initially misleading: piping a raw `full_log` string into it selects the generic `json` decoder instead of `windows_eventchannel`, which breaks any rule depending on the latter — a false negative caused by the test tool, not the rule's logic.

Dropping `wazuh-logtest` in favor of live data, a full frequency audit of every rule ID that has ever matched a real event in `archives.json` —

```
sudo python3 -c "import json,collections; c=collections.Counter(json.loads(l).get('rule',{}).get('id') for l in open('/var/ossec/logs/archives/archives.json') if l.strip()); print(c.most_common(20))"
```

— showed zero matches anywhere in the `18xxx` rule range. The entire stock Sysmon rule chain has never fired once in this lab, on any event, independent of this custom rule entirely. The most likely explanation: rule 184665 matches against a legacy plain-text log format (`<match>Microsoft-Windows-Sysmon/Operational: INFORMATION(1)</match>`) that predates this Wazuh version's native JSON eventchannel decoding — the text it's looking for may simply never exist in this pipeline.

The rule was rewritten to drop that dependency entirely, matching directly on `<decoded_as>windows_eventchannel</decoded_as>` plus the `eventID` and `commandLine` fields — mechanisms each independently confirmed to work. This version still does not fire on live, regenerated events, for a reason not yet identified.

### Status

**Open.** The audit-policy-based path (Finding 2) already closes the incident-report gap this started from; this was an attempt at a stronger, audit-policy-independent layer on top of it, and remains unfinished. Investigation was paused here rather than continued indefinitely.

---

## Lessons Learned

- An empty Wazuh dashboard search is not proof of a missing rule. Check `archives.json` or search by `rule.id` directly before concluding a detection gap exists — this caught Finding 1 and pointed straight to the real cause behind Finding 3.
- Check for existing stock rule coverage before writing a custom rule. Two of the three "gaps" investigated here turned out to already be solved by Wazuh's built-in ruleset.
- A tool built for testing rules (`wazuh-logtest`) can introduce its own confound — its decoder selection on a bare replayed log differs from live processing. Verify findings against live data, not just the test tool, before trusting a negative result.
- Chaining a custom rule off an existing `if_sid` is only as reliable as the rule it depends on. Confirm the parent actually fires on live data before building on top of it.
- Not every detection effort resolves cleanly. Documenting a dead end accurately, with the diagnostic trail intact, is itself a defensible piece of detection-engineering work.

---

*Part of the SOC Detection Lab portfolio project hardening pass, 2026-06-22. All activity was performed in a private, isolated, locally hosted lab environment.*
