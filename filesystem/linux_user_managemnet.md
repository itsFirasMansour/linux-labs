

# Linux Permissions & User Management Cheat Sheet

## 1. File Permissions Mental Model

* **Structure:** `[type][owner][group][others]`
* Example: `-rwxr-xr-- 1 firas devs file.sh`


* **Types:** `-` (file), `d` (dir), `l` (symlink).
* **Numeric (Octal) Values:**
* `4`: Read (r)
* `2`: Write (w)
* `1`: Execute (x)
* *Calculation: Sum the numbers (e.g., 7 = 4+2+1)*



## 2. `chmod` (Changing Permissions)

### Numeric Mode

* `chmod 755 file` (rwxr-xr-x)
* `chmod 644 file` (rw-r--r--)
* `chmod 700 dir`  (rwx------)
* `chmod 600 file` (rw-------)

### Symbolic Mode

* `chmod u+x file`  # Add execute for owner
* `chmod g-w file`  # Remove write from group
* `chmod o-r file`  # Remove read from others
* `chmod a+x file`  # Execute for everyone
* `chmod u=rw,go=r file` # Set exactly

| Mode | Symbolic | Use Case |
| --- | --- | --- |
| 644 | rw-r--r-- | Typical file |
| 755 | rwxr-xr-x | Script / Dir |
| 700 | rwx------ | Private dir |
| 600 | rw------- | SSH private key |
| 770 | rwxrwx--- | Shared group dir |

## 3. `chown` (Ownership)

* `chown user file`          # Change owner
* `chown user:group file`     # Change owner and group
* `chown :group file`        # Change group only
* `chown -R user:group /dir` # Recursive change : The -R flag stands for Recursive. When applied to a directory, it changes the ownership of that directory and every single file, subdirectory, and hidden file contained inside it.

## 4. `useradd` & `usermod`

### Create Users

* `sudo useradd -m -s /bin/bash alice` # Create home + shell
* `sudo useradd -m -G devs alice`      # Add to supplementary group

### Modify Users

* `sudo usermod -aG sudo alice`        # Append to group (KEEP existing)
* `sudo usermod -L alice`              # Lock account
* `sudo usermod -s /bin/zsh alice`     # Change shell

| Flag | Meaning |
| --- | --- |
| `-m` | Create home directory |
| `-s` | Set login shell |
| `-G` | Add to supplementary group |
| `-aG` | Append to group (don't replace) |
| `-r` | System user (UID < 1000) |

## 5. Deletion & Verification

* `sudo userdel -r alice`              # Delete user + home dir
* `sudo groupadd -g 1500 devs`         # Create group with specific GID
* `id alice`                           # Show UID, GID, and groups

## 6. `/etc/passwd` & `/etc/shadow`

### `/etc/passwd` Structure

`username:x:UID:GID:GECOS:home_dir:shell`

* `UID 0`: Root
* `< 1000`: System users
* `≥ 1000`: Regular users

### `/etc/shadow` Structure

`username:hash:lastchg:min:max:warn:inactive:expire`

* `!!` or `!`: Account is locked.
* `*`: No password set.

## 7. Security Notes & Pitfalls

* **The `-aG` Trap:** `usermod -G` replaces groups; `usermod -aG` appends to them.
* **SSH Keys:** Must be `600` permissions.
* **System Users:** Typically use `/sbin/nologin` shell to prevent interactive login.
* **World-Writable:** Never set `777` on files or directories in production.
* **Root Only:** `/etc/shadow` should be readable by root only.
