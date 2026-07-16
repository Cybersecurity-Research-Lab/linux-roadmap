# 📑 Module Cheat Sheet

One-page quick reference for every command covered in this module. Keep this open in a second window while you work.

---

## 👤 Users and Groups

```bash
# Identity
id                              # UID, GID, groups for current user
id username                     # same, for another user
whoami                          # current username
groups [username]               # list group memberships

# User lifecycle
sudo useradd -m -s /bin/bash user    # create user + home dir + shell
sudo passwd user                     # set password
sudo usermod -L user                 # lock account
sudo usermod -U user                 # unlock account
sudo userdel user                    # delete user
sudo userdel -r user                 # delete user + home dir

# Groups
sudo groupadd groupname              # create group
sudo usermod -aG groupname user      # add user to group (ALWAYS use -aG)
sudo gpasswd -d user groupname       # remove user from group
sudo groupdel groupname              # delete group

# Key files
/etc/passwd     # user accounts
/etc/shadow     # password hashes (root only)
/etc/group      # group definitions

# Security audit
awk -F: '$3 == 0 {print $1}' /etc/passwd    # find all UID-0 accounts
```

---

## 🔐 File Permissions

```bash
# Read permissions
ls -l file                # long listing
ls -ld directory           # directory itself, not contents
stat file                  # detailed, includes octal

# Change permissions
chmod 755 file             # octal
chmod u+x file              # symbolic: add execute for owner
chmod go-w file              # remove write from group+other
chmod -R 750 dir              # recursive

# Change ownership
sudo chown user file
sudo chown user:group file
sudo chgrp group file
sudo chown -R user:group dir

# Special bits
chmod u+s file    # SUID   (4xxx)
chmod g+s dir      # SGID   (2xxx)
chmod +t dir        # sticky (1xxx)

# umask
umask               # show current
umask 022            # set for session

# Security audit
find / -perm -4000 -type f 2>/dev/null    # all SUID binaries
find / -perm -2000 -type f 2>/dev/null    # all SGID binaries
find / -writable -type d 2>/dev/null      # world-writable directories

# Octal reference
rwx=7  rw-=6  r-x=5  r--=4  -wx=3  -w-=2  --x=1  ---=0
```

---

## ⚙️ Processes

```bash
# Inspect
ps aux                        # all processes
ps aux --sort=-%cpu           # sorted by CPU
ps auxf                        # tree view
pstree -p                       # tree view, alternate tool
top / htop                       # live view
lsof -p PID                       # open files/sockets for a process

# /proc detail
cat /proc/PID/cmdline
ls -l /proc/PID/exe
cat /proc/PID/environ

# Control
kill -15 PID          # SIGTERM (graceful, default)
kill -9 PID            # SIGKILL (force)
kill -1 PID              # SIGHUP (often = reload)
killall processname        # signal by name
pkill -f "pattern"           # signal by command-line match

# Job control
command &        # background
jobs                 # list background jobs
fg %1                  # foreground job 1
bg %1                    # resume stopped job in background
Ctrl+Z                     # suspend foreground job
Ctrl+C                       # SIGINT foreground job

# Priority
nice -n 10 command       # start with adjusted priority
renice 10 -p PID            # adjust running process priority

# Security audit
ps auxf | less              # look for unexpected parent/child chains
ls -l /proc/PID/exe          # confirm what a process is actually running
```

---

## 🧩 Services (systemd)

```bash
# Lifecycle
systemctl start name
systemctl stop name
systemctl restart name
systemctl reload name
systemctl enable name              # start at boot (doesn't start now)
systemctl disable name
systemctl enable --now name         # enable AND start

# Status
systemctl status name
systemctl is-active name
systemctl is-enabled name
systemctl --failed                    # all failed units
systemctl list-units --type=service --state=running
systemctl list-unit-files --state=enabled

# Logs
journalctl -u name
journalctl -u name -f                  # follow live
journalctl -u name --since today
journalctl -p err                        # errors and above, all units
journalctl -b                              # since current boot

# Unit files
systemctl daemon-reload           # ALWAYS after editing a unit file
systemctl cat name                  # show resolved unit content
systemctl edit name                   # create safe override

# Minimal unit file skeleton
# /etc/systemd/system/name.service
[Unit]
Description=...
After=network.target

[Service]
Type=simple
User=appuser
ExecStart=/path/to/binary
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

---

## 📦 Package Managers

```bash
# --- Debian / Ubuntu (apt, dpkg) ---
sudo apt update                  # refresh index (no install)
sudo apt upgrade                  # upgrade all packages
sudo apt install pkg                # install
sudo apt remove pkg                   # remove, keep config
sudo apt purge pkg                      # remove + config
sudo apt autoremove                       # remove unneeded deps
apt list --installed
apt list --upgradable
apt search keyword
apt show pkg
dpkg -l                              # list installed
dpkg -L pkg                            # files owned by pkg
dpkg -S /path/to/file                    # which pkg owns this file

# --- RHEL / Fedora (dnf/yum, rpm) ---
sudo dnf check-update
sudo dnf upgrade
sudo dnf install pkg
sudo dnf remove pkg
dnf list installed
dnf search keyword
dnf info pkg
rpm -qa                              # list installed
rpm -ql pkg                            # files owned by pkg
rpm -qf /path/to/file                    # which pkg owns this file

# Repositories
/etc/apt/sources.list, /etc/apt/sources.list.d/    # Debian/Ubuntu
/etc/yum.repos.d/                                    # RHEL/Fedora
```

---

## 🌐 Environment Variables

```bash
printenv                # list all env vars
printenv VAR              # show one
echo $VAR                   # reference a variable
env                            # list env vars / run cmd with modified env
set                               # list ALL vars incl. shell-local

VAR="value"                # shell-local (not inherited)
export VAR="value"           # exported (inherited by children)
unset VAR                       # remove

VAR="value" command                # set for one command only
env -i command                        # run with empty environment

# Key variables
PATH      # executable search path (order matters!)
HOME      # home directory
USER      # username
SHELL     # login shell
PWD       # current directory
EDITOR    # default editor

# Persistence
/etc/environment       # system-wide
/etc/profile              # system-wide, login shells
~/.bash_profile             # per-user, login shells
~/.bashrc                     # per-user, interactive shells
source ~/.bashrc                # reload without new session

# Add to PATH persistently (append, not prepend, for safety)
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.bashrc
```

---

## 🔀 Shell Redirection & Pipes

```bash
# Redirection
command > file          # stdout, overwrite
command >> file           # stdout, append
command < file               # stdin from file
command 2> file                 # stderr only
command > file 2>&1               # stdout + stderr, SAME file (order matters!)
command &> file                     # shorthand for above (bash)
command > /dev/null 2>&1              # discard everything

# Pipes
cmd1 | cmd2                   # cmd1's stdout -> cmd2's stdin
cmd1 | tee file | cmd2          # save to file AND continue pipeline

# Common text-processing toolkit
grep pattern              # filter matching lines (-v invert, -i case-insens, -c count)
awk '{print $1}'             # print field 1 (whitespace-delimited)
sed 's/old/new/g'               # substitute
sort / sort -n / sort -r           # sort (numeric / reverse)
uniq -c                               # count adjacent duplicates (sort first!)
wc -l                                    # count lines
cut -d: -f1                                # extract field 1, ':' delimiter
xargs                                        # build args from piped input

# Example pipeline: top failed-SSH source IPs
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn | head -10
```

---

## 🚨 Quick Security Audit Checklist

```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd          # duplicate UID 0 accounts
find / -perm -4000 -type f 2>/dev/null              # SUID binaries
find / -writable -type d 2>/dev/null                  # world-writable dirs
ps auxf                                                  # process tree anomalies
systemctl list-unit-files --state=enabled                 # what persists at boot
systemctl --failed                                           # failed services
apt list --upgradable   /   dnf check-update                   # patch status
grep -r "^deb " /etc/apt/sources.list*   /   ls /etc/yum.repos.d/   # repo review
echo $PATH                                                             # PATH order review
```
