# Sample Logs

This directory contains sanitised and anonymised log samples collected from the lab during attack simulations.

> **Important:** Do not commit real log files containing sensitive information such as real IP addresses, hostnames, usernames, or other personally identifiable data. Sanitise all logs before committing. Replace real values with clearly marked placeholders (e.g., `[REDACTED-IP]`, `[HOSTNAME]`).

## Structure

```
sample-logs/
├── windows/          # Windows Security Event Log exports (.evtx or .xml)
├── linux/            # Linux auth.log and syslog samples
├── wazuh-alerts/     # Wazuh alert JSON exports
└── splunk-exports/   # Splunk search result exports
```

## Adding Logs

1. Export the relevant log entries from your SIEM or log source
2. Sanitise sensitive fields
3. Save to the appropriate subdirectory with a descriptive filename (e.g., `4625-brute-force-sample.xml`)
4. Reference the file in the relevant attack scenario or incident report

[Add your sanitised log samples to the subdirectories here.]
