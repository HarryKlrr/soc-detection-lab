# Windows Security Event ID Reference

A reference guide to key Windows Security Event IDs relevant to SOC analysis and the scenarios in this lab.

---

## Authentication Events

### Event ID 4624 — Successful Logon

**Description:** A user account was successfully logged on.

**Key fields to examine:**
- `Account Name` — the account that logged on
- `Logon Type` — how the logon occurred (see table below)
- `Source Network Address` — IP address of the source (remote logons)
- `Workstation Name` — name of the machine the logon originated from

**Logon Types:**

| Type | Description |
|---|---|
| 2 | Interactive (local console) |
| 3 | Network (e.g., file share, SMB) |
| 4 | Batch (scheduled task) |
| 5 | Service |
| 7 | Unlock |
| 10 | RemoteInteractive (RDP) |
| 11 | CachedInteractive |

**SOC relevance:** Look for logons at unusual times, from unexpected IP addresses, or using service accounts interactively.

[Add an example log entry from your lab here.]

---

### Event ID 4625 — Failed Logon

**Description:** An account failed to log on.

**Key fields to examine:**
- `Account Name` — the targeted account
- `Source Network Address` — origin of the attempt
- `Failure Reason` — why the logon failed (e.g., bad password, account locked)
- `Logon Type` — how the logon was attempted

**SOC relevance:** High volumes of 4625 events from a single source IP within a short time window indicate a brute force or password spray attempt.

[Add an example log entry from your lab here.]

---

### Event ID 4672 — Special Privileges Assigned to New Logon

**Description:** Special privileges (e.g., SeDebugPrivilege, SeBackupPrivilege) were assigned to a new logon session.

**SOC relevance:** Indicates a privileged account has logged on. Correlate with 4624 to understand the full logon context. Unexpected use of privileged accounts outside maintenance windows warrants investigation.

[Add an example log entry from your lab here.]

---

## Process Events

### Event ID 4688 — A New Process Has Been Created

**Description:** A new process was created on the system.

**Key fields to examine:**
- `New Process Name` — path and name of the new process
- `Creator Process Name` — parent process
- `Process Command Line` — full command line (requires audit policy: `Audit Process Creation — include command line`)
- `Subject Account Name` — user context

**SOC relevance:** Used to detect unusual process executions — e.g., `cmd.exe` or `powershell.exe` spawned by `winword.exe` or another Office application.

[Add an example log entry from your lab here.]

---

## Account Management Events

### Event ID 4720 — A User Account Was Created

**Description:** A new local or domain user account was created.

**Key fields to examine:**
- `New Account Name` — the created account
- `Subject Account Name` — the account that performed the action
- `Target Domain` — local or domain context

**SOC relevance:** Unexpected account creation — especially outside of a change management window — may indicate persistence activity.

[Add an example log entry from your lab here.]

---

### Event ID 4728 — A Member Was Added to a Security-Enabled Global Group

**Description:** A user was added to a security-enabled global group (e.g., Domain Admins).

**Key fields to examine:**
- `Member Name` — the account added
- `Group Name` — the group the account was added to
- `Subject Account Name` — who performed the action

**SOC relevance:** Additions to privileged domain groups (Domain Admins, Enterprise Admins) should be rare and always authorised.

[Add an example log entry from your lab here.]

---

### Event ID 4732 — A Member Was Added to a Security-Enabled Local Group

**Description:** A user was added to a local security group (e.g., Administrators).

**Key fields:** Same as 4728 but for local groups.

**SOC relevance:** Adding an account to the local Administrators group is a common persistence technique. Alert on unexpected additions.

[Add an example log entry from your lab here.]

---

## Security Tool Events

### Event ID 5007 — Microsoft Antimalware Configuration Changed

**Description:** A change was made to Windows Defender or Microsoft antimalware configuration.

**Key fields to examine:**
- `Old Value` — previous configuration setting
- `New Value` — new configuration setting (look for values indicating protection was disabled)

**SOC relevance:** Changes that disable real-time protection, cloud-delivered protection, or tamper protection are a strong indicator of defence evasion.

[Add an example log entry from your lab here.]

---

## Service Events

### Event ID 7045 — A New Service Was Installed

**Description:** A new service was installed on the system (recorded in the System log, not the Security log).

**Key fields to examine:**
- `Service Name` — name of the installed service
- `Service File Name` — path to the service executable
- `Service Type` — kernel driver, user mode service, etc.
- `Service Account` — account the service runs under

**SOC relevance:** Malware and attackers frequently install malicious services for persistence. Alert on new services with unusual names, paths in temp directories, or services running as SYSTEM.

[Add an example log entry from your lab here.]

---

## Audit Events

### Event ID 1102 — The Audit Log Was Cleared

**Description:** The Windows Security event log was cleared.

**Key fields to examine:**
- `Subject Account Name` — who cleared the log
- Timestamp

**SOC relevance:** Clearing event logs is a common anti-forensics technique. Any occurrence outside of an authorised maintenance window should be treated as a high-priority alert.

[Add an example log entry from your lab here.]

---

## Summary Table

| Event ID | Description | Key Relevance |
|---|---|---|
| 4624 | Successful logon | Baseline, lateral movement |
| 4625 | Failed logon | Brute force, password spray |
| 4672 | Special privileges assigned | Privileged account activity |
| 4688 | Process created | Malicious execution, LOLBins |
| 4720 | User account created | Persistence |
| 4728 | Added to global group | Privilege escalation |
| 4732 | Added to local group | Persistence, privilege escalation |
| 5007 | Defender config changed | Defence evasion |
| 7045 | Service installed | Persistence |
| 1102 | Audit log cleared | Anti-forensics |
