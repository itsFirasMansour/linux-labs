# SSH Fundamentals

## What is SSH?

**SSH (Secure Shell)** is a secure network protocol used to remotely access and manage another computer over an encrypted connection.

Common uses:

* Remote server administration
* Secure file transfers (SCP/SFTP)
* Running commands remotely
* Secure tunneling and port forwarding

Default SSH port:

```bash
22
```

---

# Asymmetric Encryption (Public Key Cryptography)

SSH relies heavily on **asymmetric encryption**.

Unlike traditional password authentication, asymmetric encryption uses **two mathematically linked keys**:

* Public Key
* Private Key

These keys are generated together (commonly using RSA, Ed25519, or ECDSA algorithms).

Properties:

* The public key can be shared freely.
* The private key must remain secret.
* The private key cannot realistically be derived from the public key.
* Data encrypted with one key can only be decrypted using its paired key.

---

## How SSH Key Authentication Works

### Step 1 — Generate Key Pair

On the client machine:

```bash
ssh-keygen
```

This creates:

```text
id_rsa        -> Private Key
id_rsa.pub    -> Public Key
```

or

```text
id_ed25519
id_ed25519.pub
```

for Ed25519 keys.

---

### Step 2 — Copy Public Key to Server

The public key is placed inside:

```bash
~/.ssh/authorized_keys
```

on the remote server.

---

### Step 3 — Authentication

When connecting:

```bash
ssh user@server
```

the server checks whether the client possesses the private key corresponding to the stored public key.

The private key never leaves the client machine.

Result:

```text
Public Key -> Stored on Server
Private Key -> Stays on Client
```

Because the private key is never transmitted across the network, SSH key authentication is significantly more secure than passwords.

---

# SSH Configuration Files

SSH uses different configuration files.

## Client Configuration

```bash
/etc/ssh/ssh_config
```

or

```bash
~/.ssh/config
```

Purpose:

* Controls how the SSH client behaves.

Examples:

```text
Port
IdentityFile
Host aliases
User
```

---

## Server Configuration

```bash
/etc/ssh/sshd_config
```

Purpose:

* Controls how the SSH server behaves.

Examples:

```text
PermitRootLogin
PasswordAuthentication
AllowUsers
AuthorizedKeysFile
Port
```

---

# ssh_config Configuration Priority

SSH reads settings in the following order:

1. Command-line options
2. User configuration file
3. System-wide configuration file

Example:

```bash
ssh -p 2222 user@server
```

The command-line option overrides any configuration file settings.

---

# Understanding Ports

A port is a logical communication endpoint used to direct network traffic to the correct service.

Examples:

| Service | Port |
| ------- | ---- |
| SSH     | 22   |
| HTTP    | 80   |
| HTTPS   | 443  |
| FTP     | 21   |

Think of:

```text
IP Address = Building
Port = Apartment Number
```

The IP identifies the machine.

The port identifies the service running on that machine.

---

# Port Directive

Inside:

```bash
/etc/ssh/sshd_config
```

Example:

```bash
Port 2222
```

This changes SSH from the default port 22 to port 2222.

---

## Why Change Port 22?

Benefits:

* Reduces automated bot scans.
* Reduces log spam.
* Hides SSH from basic automated attacks.

This technique is called:

```text
Security Through Obscurity
```

Important:

Changing the port does **not** truly secure SSH.

It only reduces noise.

Real security comes from:

* SSH keys
* Strong access controls
* Updated software
* Disabling password authentication

---

# PermitRootLogin

Directive:

```bash
PermitRootLogin no
```

Controls whether the root account can log in directly via SSH.

---

## Why Disable Root Login?

### Problem 1: Predictable Username

Attackers already know:

```text
root
```

They only need to guess the password.

---

### Problem 2: Full System Access

If root is compromised:

```text
Complete control of the system
```

is immediately obtained.

---

## Recommended Configuration

```bash
PermitRootLogin no
```

Instead:

1. Log in as a normal user.
2. Use sudo when administrative privileges are required.

Example:

```bash
sudo apt update
```

---

# PasswordAuthentication

Directive:

```bash
PasswordAuthentication yes
```

or

```bash
PasswordAuthentication no
```

Controls whether users can authenticate using passwords.

---

## PasswordAuthentication yes

Allows:

```text
Username + Password
```

authentication.

Risk:

* Brute-force attacks
* Credential stuffing
* Weak passwords

---

## PasswordAuthentication no

Disables password logins completely.

Only SSH keys are accepted.

Benefits:

* Stops password guessing attacks.
* Much stronger authentication.
* Preferred on production servers.

Recommended:

```bash
PasswordAuthentication no
```

---

# AuthorizedKeysFile

Directive:

```bash
AuthorizedKeysFile .ssh/authorized_keys
```

Specifies where SSH looks for authorized public keys.

Default location:

```bash
~/.ssh/authorized_keys
```

---

## Authentication Process

1. Client sends authentication request.
2. Server checks AuthorizedKeysFile.
3. Server finds matching public key.
4. Client proves ownership of corresponding private key.
5. Access granted.

If the public key is not found:

```text
Authentication Failed
```

---

# AllowUsers

Directive:

```bash
AllowUsers user1 user2 admin
```

Creates an explicit allowlist.

Only listed users may log in through SSH.

Everyone else is denied access.

---

## Example

```bash
AllowUsers firas admin_user
```

Allowed:

```text
firas
admin_user
```

Blocked:

```text
root
guest
dev_user
test_user
```

unless explicitly listed.

---

# SSH Hardening Checklist

A secure SSH configuration typically includes:

```bash
Port 2222
PermitRootLogin no
PasswordAuthentication no
AllowUsers admin_user
```

and uses:

```bash
SSH Key Authentication
```

instead of passwords.

---

# Useful Commands

Generate SSH key pair:

```bash
ssh-keygen
```

Copy public key to server:

```bash
ssh-copy-id user@server
```

Connect to server:

```bash
ssh user@server
```

Connect using custom port:

```bash
ssh -p 2222 user@server
```

Restart SSH service:

```bash
sudo systemctl restart ssh
```

Check SSH status:

```bash
sudo systemctl status ssh
```

View SSH configuration:

```bash
sudo cat /etc/ssh/sshd_config
```

---

# Key Takeaways

* SSH provides secure remote access.
* SSH key authentication is safer than passwords.
* Public keys are stored on the server.
* Private keys stay on the client.
* Disable direct root login.
* Disable password authentication when possible.
* Use AllowUsers to restrict access.
* Changing the SSH port reduces automated attacks but is not a replacement for proper security.
* SSH hardening combines multiple layers of protection rather than relying on a single setting.
