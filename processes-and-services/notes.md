# 02 — Processes & Services
**Phase:** 01 — Linux & Networking Fundamentals  
**Date:** 2026-06-18  
**Topics:** `ps aux` · `top` · `htop` · `systemctl` · `journalctl`

---

## 1. Processes — `ps aux`

`ps aux` gives a **full snapshot** of every running process on the system by reading `/proc`. It covers all users and all background services — not just your current terminal session.

```bash
ps aux          # snapshot of all processes
ps aux -He      # same, but shows parent/child hierarchy with indentation
```

### Output Columns

| Column | Meaning |
|--------|---------|
| `USER` | Account that owns and launched the process |
| `PID` | Unique process ID assigned by the kernel — used to target it (e.g. `kill 1234`) |
| `%CPU` | % of CPU currently consumed |
| `%MEM` | % of physical RAM currently consumed |
| `VSZ` | Total **virtual** memory allocated (KiB) — includes swapped-out memory, shared libs, code |
| `RSS` | Actual **physical** RAM in use (KiB) — excludes swap, better real-world indicator than VSZ |
| `TTY` | Controlling terminal (`pts/0` = SSH/terminal window, `?` = background daemon with no terminal) |
| `STAT` | Process state code (see below) |
| `START` | When the process started (`HH:MM` if today, `Mon DD` if older) |
| `TIME` | Cumulative CPU time consumed since launch (not wall-clock time) |
| `COMMAND` | Full command + arguments used to start it. `[brackets]` = kernel thread |

### STAT Codes

| Code | Meaning |
|------|---------|
| `R` | Running / runnable (on CPU or ready for it) |
| `S` | Sleeping — waiting for an event (most processes are here) |
| `D` | Uninterruptible sleep — waiting on disk I/O, **cannot be killed** |
| `T` | Stopped (suspended via `Ctrl+Z` or debugger) |
| `Z` | Zombie — process finished but parent hasn't collected its exit status yet |

**STAT modifiers** that can appear after the letter: `s` = session leader · `l` = multi-threaded · `+` = foreground process · `<` = high priority · `N` = low priority (nice)

---

## 2. Real-time Monitoring — `top`

```bash
top        # live view, updates every 3s
# While inside top:
# 1  → toggle individual CPU cores
# M  → sort by memory
# P  → sort by CPU (default)
# k  → kill a process by PID
# q  → quit
```

### Header Sections

**Load Average** (top-right of first line)
```
load average: 0.45, 0.82, 1.15
```
Three numbers = average load over last **1 min / 5 min / 15 min**.  
Load = processes actively using a CPU core + processes waiting for one.  
Rule: compare to your **core count**. On a 4-core machine, `4.00` = 100% capacity, `8.00` = overloaded.

**Tasks**
```
Tasks: 285 total, 2 running, 283 sleeping, 0 stopped, 0 zombie
```
- `running` = actively on CPU right now
- `sleeping` = waiting for something (uses RAM, 0 CPU)
- `zombie` = dead but not cleaned up — shouldn't pile up

**CPU**
```
%Cpu(s): 12.5 us, 3.2 sy, 0.0 ni, 83.1 id, 0.5 wa, 0.0 hi, 0.7 si, 0.0 st
```

| Key | Meaning |
|-----|---------|
| `us` | User processes (your apps) |
| `sy` | Kernel / system calls |
| `id` | Idle — higher is better |
| `wa` | Waiting on disk I/O — if high, storage is bottleneck |
| `st` | Steal — CPU taken by hypervisor (only relevant in VMs) |

**Memory**
```
MiB Mem:  15943.2 total,  2105.4 free,  8432.1 used,  5405.7 buff/cache
MiB Swap:  2048.0 total,  2048.0 free,     0.0 used.  7120.5 avail Mem
```
- `buff/cache` = Linux file cache — freely released when apps need RAM
- `avail Mem` = the real indicator: how much can actually be given to new processes without touching swap

---

## 3. `htop`

```bash
sudo apt install htop   # if not installed
htop
# F1 = help, F6 = sort, F9 = kill, F10 = quit
```

`htop` is `top` with a colour UI, mouse support, and easier process killing. Same data, better navigation. Use it interactively; use `top` when working on a minimal system or over SSH where htop may not be installed.

---

## 4. Services — `systemctl`

`systemd` is the init system on most modern Linux distros. It manages all background services (called **units**).

```bash
systemctl status ssh     # inspect service state + recent logs
systemctl start ssh      # start the service
systemctl stop ssh       # stop it
systemctl restart ssh    # stop + start (applies config changes, drops connections)
systemctl reload ssh     # apply config changes without dropping active connections
systemctl enable ssh     # auto-start on boot
systemctl disable ssh    # remove from boot
```

### Reading `systemctl status` Output

```
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled)
     Active: active (running) since ...
```

- **Loaded** — path to the unit file + whether it's enabled at boot
- **Active** — current state: `active (running)` / `inactive (dead)` / `failed`
- **Log tail** — last ~10 lines from journald, timestamped

---

## 5. Logs — `journalctl`

`journald` is systemd's logging daemon. `journalctl` queries it.

```bash
journalctl -xe              # jump to end of log + explain known errors (main "something broke" command)
journalctl -u ssh           # filter logs for SSH service only
journalctl -u ssh -f        # follow SSH logs live (like tail -f)
journalctl -p err           # errors only (priority 3)
journalctl -p warning       # warnings and above (priority 4)
journalctl -p info          # info and above (priority 6)
journalctl --since "1 hour ago"   # time-scoped
```

**Flag reference:**
- `-x` — adds explanation/documentation links for known error codes
- `-e` — jumps to end of log (most recent entries)
- `-u <unit>` — filter to a specific systemd service
- `-f` — follow mode (live stream)
- `-p` — filter by priority level

---

## Key Concepts

- **PID** — unique kernel-assigned number for every process. Use it to kill, inspect, or signal a process.
- **Zombie process** — a process that finished but whose parent hasn't called `wait()` to collect its exit code. Harmless in small numbers, problematic if they pile up (PID table exhaustion).
- **Daemon** — a background service process. Usually shows `?` in the TTY column and `s` in STAT (session leader).
- **Unit** — systemd's generic term for anything it manages: services (`.service`), sockets (`.socket`), timers (`.timer`), etc. The `-u` flag in journalctl targets a unit by name.
- **Load average vs CPU %** — CPU % tells you utilisation right now; load average tells you demand over time including queued processes. Both matter.

---

## Quick Reference

```bash
# Most-used combos
ps aux | grep <name>         # find a specific process
kill <PID>                   # terminate process (SIGTERM)
kill -9 <PID>               # force kill (SIGKILL) — last resort
systemctl status <service>   # check any service
journalctl -xe               # first thing to run when something breaks
journalctl -u <service> -f   # watch a service's logs live
```
