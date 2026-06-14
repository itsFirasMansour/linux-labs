# Linux User Management Cheat Sheet

## User Creation

```bash
adduser username
useradd username
```

Create a new user account.

---

## Password Management

```bash
passwd username
```

Set or change a user's password.

---

## User Deletion

```bash
userdel username
```

Delete a user.

```bash
userdel -r username
```

Delete a user and their home directory.

---

## Group Management

Create a group:

```bash
groupadd groupname
```

Delete a group:

```bash
groupdel groupname
```

---

## User and Group Information

Display user information:

```bash
id username
```

Display user groups:

```bash
groups username
```

List all users:

```bash
cat /etc/passwd
```

List all groups:

```bash
cat /etc/group
```

---

## Switching Users

Switch to another user:

```bash
su username
```

Switch to root:

```bash
su -
```

Execute a command as root:

```bash
sudo command
```

---

## Account Locking

Lock a user account:

```bash
passwd -l username
```

Unlock a user account:

```bash
passwd -u username
```

---

## Important System Files

Users:

```text
/etc/passwd
```

Encrypted passwords:

```text
/etc/shadow
```

Groups:

```text
/etc/group
```

Group passwords:

```text
/etc/gshadow
```

---

## ⚠️ Important: Adding Users to Groups

Correct command:

```bash
usermod -aG groupname username
```

Example:

```bash
usermod -aG developers firas
```

Incorrect command:

```bash
usermod -G groupname username
```

The `-G` option replaces all supplementary groups for the user.

The `-aG` option appends the new group while preserving existing memberships.

This is one of the most common Linux administration mistakes because it can silently remove a user from every other group they belong to.

---

## Exam Tips

* `adduser` is interactive and easier for beginners.
* `useradd` is lower-level and commonly used in scripts.
* User information is stored in `/etc/passwd`.
* Group information is stored in `/etc/group`.
* Password hashes are stored in `/etc/shadow`.
* Always use `usermod -aG` when adding a user to an existing group.
