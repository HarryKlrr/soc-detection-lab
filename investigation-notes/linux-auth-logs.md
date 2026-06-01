# Linux Authentication Log Reference

## Overview

Linux systems record authentication activity in log files that are essential for SOC analysis. The primary log files are:

| Log File | Distribution |
|---|---|
| `/var/log/auth.log` | Debian/Ubuntu |
| `/var/log/secure` | RHEL/CentOS/Fedora |

These logs record: SSH authentication, sudo usage, su commands, PAM events, and cron job execution.

---

## Key Log Patterns

### Successful SSH Login

```
[Timestamp] [hostname] sshd[[pid]]: Accepted password for [username] from [IP] port [port] ssh2
[Timestamp] [hostname] sshd[[pid]]: Accepted publickey for [username] from [IP] port [port] ssh2
```

**SOC relevance:** Note the source IP and username. Logins from unexpected IPs or outside business hours warrant investigation.

[Add an example entry from your lab here.]

---

### Failed SSH Login

```
[Timestamp] [hostname] sshd[[pid]]: Failed password for [username] from [IP] port [port] ssh2
[Timestamp] [hostname] sshd[[pid]]: Failed password for invalid user [username] from [IP] port [port] ssh2
```

**SOC relevance:** Multiple failed attempts from a single IP in a short window indicate a brute force attempt.

[Add an example entry from your lab here.]

---

### Invalid Username Attempt

```
[Timestamp] [hostname] sshd[[pid]]: Invalid user [username] from [IP] port [port]
```

**SOC relevance:** Attempts using common usernames (admin, root, test, ubuntu) suggest automated scanning or credential stuffing.

[Add an example entry from your lab here.]

---

### Sudo Usage

```
[Timestamp] [hostname] sudo: [username] : TTY=[tty] ; PWD=[directory] ; USER=root ; COMMAND=[command]
```

**SOC relevance:** Review commands run with sudo, especially if performed by non-standard accounts or at unusual times.

[Add an example entry from your lab here.]

---

### Account Added to sudoers / Group Change

```
[Timestamp] [hostname] usermod[[pid]]: add '[username]' to group 'sudo'
```

**SOC relevance:** Adding accounts to sudo or wheel groups is analogous to Windows Event ID 4732 — a persistence indicator.

[Add an example entry from your lab here.]

---

## Wazuh Detection

Wazuh includes built-in decoders and rules for `/var/log/auth.log`:

- **Rule 5710**: SSH login attempt with non-existent user
- **Rule 5712**: Multiple failed SSH authentication attempts (brute force threshold)
- **Rule 5715**: Successful SSH login

[Add notes on which Wazuh rules fired in your lab here.]

---

## Splunk Analysis

```spl
[Add SPL query here for searching Linux auth.log data in Splunk]
| comment "Example: index=linux_logs source=/var/log/auth.log "Failed password""
| comment "rex field=_raw "from (?<src_ip>\d+\.\d+\.\d+\.\d+)""
| comment "stats count by src_ip | sort -count"
```

[Add notes on your Splunk index and sourcetype configuration for Linux logs here.]
