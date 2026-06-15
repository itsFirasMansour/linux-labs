# Lab X - Linux Control

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

# Task 3 - Configure Sudo

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

## Challenges Encountered

| Issue | Resolution |
| ----- | ---------- |
|       |            |
|       |            |

---

## Key Concepts Learned

* Concept 1
* Concept 2
* Concept 3
* Concept 4

---

## Lessons Learned

* Lesson 1
* Lesson 2
* Lesson 3

---

## Conclusion

Summarize:

* What was accomplished
* What skills were developed
* How this knowledge applies to cybersecurity or system administration
