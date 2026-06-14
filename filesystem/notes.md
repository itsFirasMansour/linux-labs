Linux Filesystem

/ — Root

The top of everything. All files and directories live under this. Only root can write here.

/bin — Essential Binaries

Core commands available to all users: ls, cat, cp, mv, bash. Always present, even in recovery mode.

/sbin — System Binaries

Admin/system commands: fdisk, ifconfig, reboot. Meant for root, not regular users.

/etc — Configuration Files

System-wide config files. Everything from network settings (/etc/hosts, /etc/resolv.conf) to user accounts (/etc/passwd, /etc/shadow). Text-based, human-readable.

/home — User Home Directories

Each user gets a folder here: /home/firas. Personal files, configs, downloads live here.

/root — Root User's Home

The superuser's home directory. Separate from /home for security.

/var — Variable Data

Data that changes constantly: logs (/var/log), mail, databases, cache. Important for monitoring and forensics.

/tmp — Temporary Files

Wiped on reboot. Programs dump temp files here. Never store anything important.

/usr — User Programs

Most installed software goes here: /usr/bin (user binaries), /usr/lib (libraries), /usr/share (shared data). Largest directory on most systems.

/lib — Shared Libraries

Essential libraries needed by /bin and /sbin binaries. Similar to .dll files on Windows.

/dev — Device Files

Everything is a file in Linux — including hardware. /dev/sda = first hard drive, /dev/null = black hole, /dev/random = random data generator.

/proc — Process & Kernel Info

Virtual filesystem — doesn't exist on disk. Live info about running processes and kernel: /proc/cpuinfo, /proc/meminfo, /proc/net/.

/sys — System/Kernel Interfaces

Similar to /proc but structured. Exposes kernel objects, hardware info, driver settings.

/boot — Boot Files

Kernel image, bootloader (GRUB) config. Don't touch unless you know what you're doing.

/mnt & /media — Mount Points

/mnt: manually mounted filesystems (USB, extra drives). /media: auto-mounted removable media.

/opt — Optional Software

Third-party software that doesn't follow standard Linux paths installs here (e.g., some commercial tools).


Key Commands Used

bashls /              # list root directory
cat /etc/os-release   # distro info
file /bin/bash    # identify file type
less /etc/passwd  # view user accounts
ls /proc/         # running processes
