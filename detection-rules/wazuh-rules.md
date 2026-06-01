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

## Notes on Adding Custom Rules

1. Custom rules are added to `/var/ossec/etc/rules/local_rules.xml` on the Wazuh Manager
2. Rule IDs for custom rules must be between `100000` and `120000`
3. After editing, restart the Wazuh Manager: `systemctl restart wazuh-manager`
4. Test rules using `wazuh-logtest` before deploying

[Add any additional notes from your lab experience here.]
