# Splunk SPL Detection Queries

Search queries for each lab scenario. All queries are written for use in Splunk's Search & Reporting app.

> Replace `[Add SPL query here]` with your tested query once validated against real log data in the lab.

---

## Query: Nmap Port Scan Detection

**Objective:** Identify multiple connection attempts from a single source IP in a short time window.

```spl
[Add SPL query here]
| comment "Expected: search firewall or network events for high connection rate from single srcip"
| comment "Example fields: src_ip, dest_port, action=blocked or rejected"
| comment "Suggested: stats count by src_ip | where count > 50 | sort -count"
```

[Insert screenshot of Splunk search results showing Nmap scan activity here]

---

## Query: Failed Logins — Windows (Event ID 4625)

**Objective:** Detect a high volume of failed Windows logon events within a time window.

```spl
[Add SPL query here]
| comment "Expected: index=windows_events EventCode=4625"
| comment "Stats count by Account_Name, Source_Network_Address"
| comment "Filter where count > threshold (e.g., 5 in 60 seconds)"
```

[Insert screenshot of Splunk results showing failed login pattern here]

---

## Query: Failed SSH Logins — Linux

**Objective:** Detect repeated SSH authentication failures from auth.log.

```spl
[Add SPL query here]
| comment "Expected: index=linux_logs sourcetype=syslog "Failed password""
| comment "Extract src_ip using rex, stats count by src_ip"
| comment "Alert when count > threshold within time window"
```

[Insert screenshot of Splunk results showing SSH brute force pattern here]

---

## Query: Suspicious PowerShell Execution

**Objective:** Identify PowerShell processes created with suspicious arguments (encoded commands, bypass flags).

```spl
[Add SPL query here]
| comment "Expected: index=sysmon EventCode=1 process_name=powershell.exe"
| comment "Search CommandLine for -EncodedCommand OR -Bypass OR -WindowStyle Hidden"
| comment "Return: time, host, user, CommandLine"
```

[Insert screenshot of Splunk results for suspicious PowerShell here]

---

## Query: New Administrator Account Created

**Objective:** Alert when a new local user account is created and added to the Administrators group.

```spl
[Add SPL query here]
| comment "Expected: index=windows_events EventCode=4720 OR EventCode=4732"
| comment "For 4732: filter where Group_Name="Administrators""
| comment "Return: time, host, New_Account_Name, Subject_Account_Name"
```

[Insert screenshot of Splunk results for new admin account here]

---

## Query: Windows Defender Disabled

**Objective:** Detect changes to Windows Defender configuration indicating a protection feature was turned off.

```spl
[Add SPL query here]
| comment "Expected: index=windows_events EventCode=5007"
| comment "Search New_Value for keywords like "0x0" (disabled) on relevant settings"
| comment "Return: time, host, Old_Value, New_Value"
```

[Insert screenshot of Splunk results for Defender change here]

---

## General Notes

- Adjust index names (`windows_events`, `linux_logs`, `sysmon`) to match your actual Splunk index configuration
- Use `| table` at the end of queries to format results clearly for screenshots
- Save confirmed queries as Splunk saved searches or alerts for future use
