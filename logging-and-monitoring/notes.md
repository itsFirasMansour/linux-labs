# Linux Logging, Cron & Process Management Notes

**Topics:** `cron` · `crontab` · `journalctl` · `pgrep` · `kill` · `pkill`

---

# 1. Cron Jobs

Cron is Linux's task scheduler. It automatically runs commands at specific times or intervals.

## Cron Syntax

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0–7)
│ │ │ └──── Month (1–12)
│ │ └────── Day of month (1–31)
│ └──────── Hour (0–23)
└────────── Minute (0–59)
```

## Examples

```bash
* * * * * date
```

Runs every minute.

```bash
0 * * * * date
```

Runs every hour.

```bash
0 9 * * * date
```

Runs every day at 09:00.

## Managing Cron Jobs

Edit cron jobs:

```bash
crontab -e
```

List installed cron jobs:

```bash
crontab -l
```

Example cron job:

```bash
* * * * * date >> /var/log/mycron.log
```

This appends the current date and time to `mycron.log` every minute.

## Security Relevance

Cron is commonly used for:

* Automated backups
* Log rotation
* System monitoring
* Scheduled maintenance

Attackers also use cron jobs as a persistence mechanism, making them important to inspect during incident response.

---

# 2. Linux Logs

Linux records system activity in log files or the system journal.

Different logs serve different purposes.

| Log      | Purpose                                 |
| -------- | --------------------------------------- |
| auth.log | Authentication and authorization events |
| syslog   | General system and service activity     |
| kern.log | Linux kernel messages                   |
| boot.log | System startup events                   |
| wtmp     | Successful login history                |
| btmp     | Failed login attempts                   |

---

# 3. Authentication Logs (auth.log)

Authentication logs contain:

* Successful logins
* Failed logins
* SSH authentication
* sudo activity
* Session open/close events

Examples:

```text
Accepted password for kali
Failed password for root
sudo: kali : COMMAND=/usr/bin/apt update
session opened for user root
```

## Questions Answered

* Who logged in?
* Were there failed login attempts?
* Who used sudo?
* When did sessions start and end?

## Security Relevance

Authentication logs help detect:

* Brute-force attacks
* Unauthorized access
* Privilege escalation
* Suspicious administrative activity

---

# 4. System Logs (syslog)

System logs contain general operating system events.

Examples:

```text
systemd[1]: Started nginx.service
systemd[1]: Stopped nginx.service
CRON[1234]: Job executed
```

## Questions Answered

* Did a service start?
* Did a service crash?
* Did a scheduled task run?
* What was the system doing?

## Security Relevance

Useful for identifying:

* Service failures
* Unexpected restarts
* Malware persistence
* Cron-based attacks

---

# 5. Kernel Logs (kern.log)

Kernel logs contain messages generated directly by the Linux kernel.

Examples:

```text
usb 1-1: new USB device found
eth0: Link is Up
Bluetooth initialized
```

## Questions Answered

* Was hardware connected?
* Did a driver fail?
* Did a network interface change state?
* Did a hardware problem occur?

## Security Relevance

Kernel logs help investigate:

* USB device activity
* Hardware failures
* Network issues
* Low-level system events

---

# 6. journald & journalctl

Many modern Linux distributions, including Kali Linux, use **systemd-journald** instead of separate log files such as:

```text
/var/log/auth.log
/var/log/syslog
/var/log/kern.log
```

Logs are stored in the system journal and viewed using `journalctl`.

## Common Commands

```bash
journalctl
```

Display all journal entries.

```bash
journalctl -xe
```

Show recent entries and explanations.

```bash
journalctl -k
```

Display kernel messages.

```bash
journalctl -u nginx
```

Display logs for a specific service.

```bash
journalctl | grep sudo
```

Search authentication-related events.

## Navigation

When viewing logs:

| Key   | Action            |
| ----- | ----------------- |
| ↑ / ↓ | Move line by line |
| Space | Next page         |
| b     | Previous page     |
| /text | Search            |
| n     | Next match        |
| N     | Previous match    |
| q     | Quit              |

---

# 7. pgrep

`pgrep` searches the process table and returns matching process IDs (PIDs).

Examples:

```bash
pgrep firefox
```

```bash
pgrep sshd
```

```bash
pgrep -x sleep
```

Output:

```text
5231
```

The number returned is the process ID.

## Why Use pgrep?

Instead of:

```bash
ps aux | grep firefox
```

Use:

```bash
pgrep firefox
```

which is cleaner and easier.

---

# 8. kill

`kill` sends signals to a process using its PID.

Default usage:

```bash
kill PID
```

Example:

```bash
kill 5231
```

This sends **SIGTERM (15)**.

SIGTERM requests a graceful shutdown and allows the process to:

* Save data
* Close files
* Release resources

---

# 9. Common Signals

## SIGTERM (15)

```bash
kill PID
```

Gracefully terminate a process.

---

## SIGKILL (9)

```bash
kill -9 PID
```

Immediately force termination.

The process cannot ignore SIGKILL.

### Rule

```text
SIGTERM = polite request to stop
SIGKILL = force stop immediately
```

---

# 10. pkill

`pkill` kills processes directly by name.

Example:

```bash
pkill firefox
```

Instead of:

```bash
pgrep firefox
kill PID
```

you can use:

```bash
pkill firefox
```

to perform both actions in one command.

---

# 11. Typical Workflow

Find process:

```bash
pgrep sleep
```

Output:

```text
5231
```

Terminate process:

```bash
kill 5231
```

Verify:

```bash
pgrep sleep
```

No output means the process was terminated.

Workflow summary:

```text
pgrep → find process
kill  → send signal
pkill → find + kill by name
```

---

# Key Concepts

* PID = unique process identifier assigned by the kernel.
* SIGTERM = graceful shutdown request.
* SIGKILL = forced immediate termination.
* Cron automates recurring tasks.
* journalctl is the primary log viewer on modern Kali Linux systems.
* auth.log tracks authentication activity.
* syslog tracks general system activity.
* kern.log tracks kernel-generated events.
* pgrep locates processes.
* kill sends signals to processes.
* pkill kills processes directly by name.
