# Lab 1 - Linux Control 

## Objective

This lab focuses on Linux user management, permissions, sudo configuration, cron jobs, and process control.

---

# Task 1 - Create Users

## Commands Used

```bash
sudo useradd -m -s /bin/bash admin_user
sudo useradd -m -s /bin/bash dev_user 
sudo useradd -m -s /bin/bash readonly_user
```

## Verification

```bash
cat /etc/passwd
```

## Screenshot

<img width="723" height="142" alt="image" src="https://github.com/user-attachments/assets/acc05287-95be-48f3-b60f-6b9a3f494875" />

<img width="713" height="70" alt="image" src="https://github.com/user-attachments/assets/92486386-c98a-4d26-9f63-69064702a374" />


## Explanation

Three users were created to simulate different privilege levels.

---

# Task 2 - Secure Directory

## Commands Used

```bash
sudo chown admin_user:admin_user /secure_data
sudo chmod 700 /secure_data
```

## Verification

```bash
ls -ld /secure_data
sudo -u dev_user ls /secure_data
sudo -u admin_user ls /secure_data
```

## Screenshot
<img width="713" height="202" alt="image" src="https://github.com/user-attachments/assets/409b69e8-7f1f-454d-980c-d1323bef1348" />

<img width="717" height="125" alt="image" src="https://github.com/user-attachments/assets/c0b3436d-5390-4551-97b3-dc77025e59bc" />


## Explanation

Ownership (`chown`) and permissions (`chmod 700`) work together to restrict access.
`700` means only the owner gets `rwx` — group and others get nothing. We verify by
testing access as `dev_user` (should fail) and `admin_user` (should succeed). This is
discretionary access control (DAC) — the same pattern used for SSH keys (`600`),
SSL certificates, and database directories in real systems.

---

# Task 3 - Cron Job

## Commands Used

```bash
sudo usermod -aG sudo admin_user
```

## Verification

```bash
id admin_user
id dev_user
```

## Screenshot

<img width="705" height="42" alt="image" src="https://github.com/user-attachments/assets/e2bd5d2d-05c4-4a43-b838-7ed24d1265bc" />

<img width="720" height="122" alt="image" src="https://github.com/user-attachments/assets/c72622e2-c86f-4a76-9071-d025e9ad75a5" />



## Explanation

`usermod -aG sudo admin_user` adds `admin_user` to the sudo group. We use `-aG` not
`-G` because `-G` alone replaces all existing groups — `-a` appends instead, keeping
current memberships intact. We verify with `id` to confirm `admin_user` has `27(sudo)`
and `dev_user` does not. This is the principle of least privilege — only users who
need elevated access get it, which limits damage if an account is compromised.

---

# Task 4 - Configure Sudo

## Commands Used

```bash
sudo touch /var/log/mycron.log
sudo chmod 666 /var/log/mycron.log
echo "* * * * * date >> /var/log/mycron.log" | sudo crontab -
```

## Verification

```bash
sudo crontab -l
cat /var/log/mycron.log
```

## Screenshot

<img width="718" height="157" alt="image" src="https://github.com/user-attachments/assets/96b1982c-ee10-429f-8a96-febaa6f6bd04" />

<img width="652" height="60" alt="image" src="https://github.com/user-attachments/assets/50f5dd61-b442-4e72-aaa7-1b338f50d5c2" />

<img width="676" height="106" alt="image" src="https://github.com/user-attachments/assets/f16ac393-aaa9-4417-87a0-ba6e38c228d2" />


## Explanation

Created `mycron.log` in `/var/log` — Linux's standard log directory. Set `chmod 666`
so both cron and our user can write to it. Registered a cron job with `* * * * *`
(every minute) to append the current timestamp. `>>` appends — `>` would overwrite
previous entries. Cron is used in real systems to automate backups, clear temp files,
and monitor services on a schedule.

---

# Task 5 - Process Kill Script

## Commands Used
```bash
sudo tee /usr/local/bin/kill_process.sh << 'EOF'
#!/bin/bash
if [ -z "$1" ]; then
    echo "Usage: $0 <process_name>"
    exit 1
fi
PROCESS_NAME="$1"
if pgrep -x "$PROCESS_NAME" > /dev/null; then
    echo "[+] Found '$PROCESS_NAME'. Killing..."
    pkill -x "$PROCESS_NAME"
    sleep 3
    if pgrep -x "$PROCESS_NAME" > /dev/null; then
        pkill -9 -x "$PROCESS_NAME"
        echo "[+] Force killed."
    else
        echo "[+] Terminated successfully."
    fi
else
    echo "[-] No process named '$PROCESS_NAME' found."
fi
EOF

sudo chmod +x /usr/local/bin/kill_process.sh
```

## Verification
```bash
sleep 300 &
pgrep -x sleep
kill_process.sh sleep
pgrep -x sleep
kill_process.sh fakeprogramxyz
```

## Screenshot
<img width="712" height="92" alt="image" src="https://github.com/user-attachments/assets/530da8a4-8e76-4f55-b983-48d7bf61fa3f" />

<img width="690" height="62" alt="image" src="https://github.com/user-attachments/assets/f13c727c-71b8-402b-a733-21ee203cb84b" />



## Explanation
The script kills a process by name using `pkill`. We use `-x` for exact name match —
without it, searching for "fire" could kill "firefox" unintentionally. The script first
sends SIGTERM, which gives the process time to save data and shut down gracefully.
`sleep 3` waits 3 seconds before checking again — if the process is still running,
SIGKILL forces it to stop immediately with no cleanup. This is used in real systems
when automating service restarts or cleaning up stuck processes.

---

## Key Concepts Learned
- Linux users each have a unique UID, home directory, and login shell
- File permissions are controlled by two things together: ownership (`chown`) and mode (`chmod`)
- `chmod 700` restricts access to the owner only — the foundation of DAC
- `-aG` appends to groups; `-G` alone replaces them — a critical difference
- Least privilege: users only get the access they actually need
- Cron automates recurring tasks using a 5-field time syntax (`* * * * *`)
- `>>` appends to files; `>` overwrites — always use `>>` for logs
- SIGTERM gives a process time to clean up; SIGKILL forces immediate termination

---

## Lessons Learned
- Always verify permissions by testing as the restricted user, not as root
- Root bypasses all permissions — `sudo -u username command` is the correct way to test
- Small mistakes like `-G` instead of `-aG` can silently break a user's access
- Automating tasks with cron requires the log file to exist and be writable beforehand

---

## Conclusion
This lab covered the core of Linux system administration: creating users with different privilege levels, restricting directory access using ownership and permissions, granting selective sudo access, automating tasks with cron, and writing a process management script. These skills directly apply to SOC and cloud security work — access control, least privilege, and log management are foundational concepts in every security role.
* How this knowledge applies to cybersecurity or system administration
