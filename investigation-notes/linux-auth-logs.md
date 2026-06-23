# Linux Authentication Log Reference

## Overview

Linux systems record authentication activity in log files that are essential for SOC analysis. The primary log files are:

| Log File | Distribution |
|---|---|
| `/var/log/auth.log` | Debian/Ubuntu |
| `/var/log/secure` | RHEL/CentOS/Fedora |

These logs record: SSH authentication, sudo usage, su commands, PAM events, and cron job execution.

> **Note:** The Linux target VM was planned for this lab (see `../lab-setup/network-design.md`) but never actually built — no scenario in this lab generated real Linux auth log data. The patterns below are reference material for SOC analysis on this log source generally, not confirmed examples from this lab.

---

## Key Log Patterns

### Successful SSH Login

```
[Timestamp] [hostname] sshd[[pid]]: Accepted password for [username] from [IP] port [port] ssh2
[Timestamp] [hostname] sshd[[pid]]: Accepted publickey for [username] from [IP] port [port] ssh2
```

**SOC relevance:** Note the source IP and username. Logins from unexpected IPs or outside business hours warrant investigation.

---

### Failed SSH Login

```
[Timestamp] [hostname] sshd[[pid]]: Failed password for [username] from [IP] port [port] ssh2
[Timestamp] [hostname] sshd[[pid]]: Failed password for invalid user [username] from [IP] port [port] ssh2
```

**SOC relevance:** Multiple failed attempts from a single IP in a short window indicate a brute force attempt.

---

### Invalid Username Attempt

```
[Timestamp] [hostname] sshd[[pid]]: Invalid user [username] from [IP] port [port]
```

**SOC relevance:** Attempts using common usernames (admin, root, test, ubuntu) suggest automated scanning or credential stuffing.

---

### Sudo Usage

```
[Timestamp] [hostname] sudo: [username] : TTY=[tty] ; PWD=[directory] ; USER=root ; COMMAND=[command]
```

**SOC relevance:** Review commands run with sudo, especially if performed by non-standard accounts or at unusual times.

---

### Account Added to sudoers / Group Change

```
[Timestamp] [hostname] usermod[[pid]]: add '[username]' to group 'sudo'
```

**SOC relevance:** Adding accounts to sudo or wheel groups is analogous to Windows Event ID 4732 — a persistence indicator.

---

## Wazuh Detection

Wazuh includes built-in decoders and rules for `/var/log/auth.log`:

- **Rule 5710**: SSH login attempt with non-existent user
- **Rule 5712**: Multiple failed SSH authentication attempts (brute force threshold)
- **Rule 5715**: Successful SSH login

None of these rules fired in this lab — the Linux target was never built, so `/var/log/auth.log` was never generated or ingested. These are the relevant stock rules to expect if the Linux target is added in a future phase.
