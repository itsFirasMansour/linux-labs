# SSH Cheat Sheet

## What is SSH?

SSH (Secure Shell) is a secure protocol used to remotely access and manage another computer over an encrypted connection.

Default port:

```bash
22
```

---

# Core SSH Commands

## Generate SSH Key Pair

```bash
ssh-keygen
```

Creates a public/private key pair for authentication.

Common files:

```text
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

or

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

---

## Copy Public Key to a Server

```bash
ssh-copy-id user@host
```

Copies your public key into the server's:

```text
~/.ssh/authorized_keys
```

This allows passwordless authentication.

---

## Connect to a Remote Host

```bash
ssh user@host
```

Example:

```bash
ssh firas@192.168.1.100
```

---

## Connect Using a Custom Port

```bash
ssh -p 2222 user@host
```

Example:

```bash
ssh -p 2222 firas@192.168.1.100
```

---

## Securely Copy Files (SCP)

Copy local file to remote server:

```bash
scp file.txt user@host:/home/user/
```

Copy remote file to local machine:

```bash
scp user@host:/home/user/file.txt .
```

Copy an entire directory:

```bash
scp -r myfolder user@host:/home/user/
```

---

# Important SSH Configuration Options

File:

```bash
/etc/ssh/sshd_config
```

---

## Port

```bash
Port 2222
```

Changes the SSH listening port from the default 22 to another port.

---

## PermitRootLogin

```bash
PermitRootLogin no
```

Prevents direct SSH login as the root user.

---

## PasswordAuthentication

```bash
PasswordAuthentication no
```

Disables password logins and forces SSH key authentication.

---

## AuthorizedKeysFile

```bash
AuthorizedKeysFile .ssh/authorized_keys
```

Specifies where SSH stores and checks authorized public keys.

---

## AllowUsers

```bash
AllowUsers admin_user firas
```

Only listed users can log in via SSH.

---

# Useful SSH Service Commands

Check SSH service status:

```bash
sudo systemctl status ssh
```

Restart SSH service:

```bash
sudo systemctl restart ssh
```

Start SSH service:

```bash
sudo systemctl start ssh
```

Enable SSH at boot:

```bash
sudo systemctl enable ssh
```

---

# SSH Authentication Flow

```text
1. Client connects to server
2. Server checks authorized_keys
3. Matching public key found
4. Client proves ownership of private key
5. Access granted
```

Important:

```text
Public Key -> Stored on Server
Private Key -> Stays on Client
```

The private key should never be shared.

---

# Troubleshooting SSH

## Error: Permission denied (publickey)

Example:

```text
Permission denied (publickey).
```

Possible causes:

* Public key not copied to server
* Wrong private key being used
* Incorrect file permissions

Fixes:

```bash
ssh-copy-id user@host
```

Check permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Verify key exists:

```bash
ls ~/.ssh
```

Debug connection:

```bash
ssh -v user@host
```

---

## Error: Connection refused

Example:

```text
ssh: connect to host 192.168.1.100 port 22: Connection refused
```

Possible causes:

* SSH service not running
* Wrong port number
* Firewall blocking access

Fixes:

Check service:

```bash
sudo systemctl status ssh
```

Start service:

```bash
sudo systemctl start ssh
```

Verify port:

```bash
sudo ss -tulpn | grep ssh
```

---

## Error: Host key verification failed

Example:

```text
Host key verification failed.
```

Cause:

The server's host key changed or the known_hosts entry is outdated.

Fix:

Remove old host key:

```bash
ssh-keygen -R hostname
```

Reconnect and accept the new key.

---

## Error: No such identity / Key not found

Example:

```text
No such identity: ~/.ssh/id_rsa
```

Cause:

SSH cannot locate the specified key.

Fixes:

List available keys:

```bash
ls ~/.ssh
```

Specify correct key:

```bash
ssh -i ~/.ssh/id_ed25519 user@host
```

Generate a new key if necessary:

```bash
ssh-keygen
```

---

# SSH Hardening Checklist

Recommended settings:

```bash
Port 2222
PermitRootLogin no
PasswordAuthentication no
AllowUsers admin_user
```

Use:

```text
✓ SSH Keys
✓ Least Privilege
✓ Restricted Users
✓ Updated System
✓ Strong File Permissions
```

Avoid:

```text
✗ Root Login
✗ Weak Passwords
✗ Exposing SSH Unnecessarily
```

---

# Quick Reference

```bash
ssh-keygen                    # Generate key pair
ssh-copy-id user@host         # Copy public key
ssh user@host                 # Connect to host
ssh -p 2222 user@host         # Connect using custom port
scp file user@host:/path      # Upload file
scp user@host:file .          # Download file
systemctl status ssh          # Check SSH service
systemctl restart ssh         # Restart SSH
```
