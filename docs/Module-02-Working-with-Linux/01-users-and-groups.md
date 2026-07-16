# 📖 Lesson 01 – Users and Groups

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain how Linux identifies users and groups internally (UID/GID)
- Read and interpret `/etc/passwd`, `/etc/shadow`, and `/etc/group`
- Create, modify, lock, and delete user accounts
- Create and manage groups, and add/remove users from them
- Identify privilege escalation risks tied to user and group misconfiguration

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟢 Beginner

## Prerequisites
- Comfortable typing commands in a Linux terminal
- Know how to use `sudo`
- No prior Linux administration experience required

## Key Terms
| Term | Definition |
|---|---|
| **UID** | Numeric User ID that uniquely identifies a user account |
| **GID** | Numeric Group ID that uniquely identifies a group |
| **Primary group** | The default group assigned to a user, recorded in `/etc/passwd` |
| **Secondary group** | Any additional group a user belongs to, recorded in `/etc/group` |
| **Root** | The superuser account, UID 0, with unrestricted system access |
| **Shadow file** | `/etc/shadow`, stores hashed passwords and account aging data, readable only by root |
| **Service account** | A non-human account used to run a specific service or daemon |

## Introduction
Every process, file, and permission decision in Linux traces back to a single question: **which user is asking?** Users and groups are the foundation identity layer that every other access-control mechanism — file permissions, sudo rules, service accounts — is built on top of. Before you can understand *why* a file is unreadable or *why* a process has the access it has, you need to understand who Linux thinks is making the request.

This lesson covers how Linux stores and manages identity: the account database files, the tools used to create and modify accounts, and the group model that lets Linux express "these five people should all have the same access" without repeating permission logic five times.

## Why This Matters
Nearly every security incident involving a Linux host eventually touches user and group configuration: an attacker creating a hidden backdoor account, a service running with more privilege than it needs, a departing employee's account that was never disabled, or a shared group membership that grants access nobody intended. If you can't confidently audit `/etc/passwd` and `/etc/group`, you can't confidently assess a system's security posture.

## Cybersecurity Insight
Attackers who gain root access frequently create a new user account as a persistence mechanism — it's quieter than modifying `/etc/sudoers` and survives reboots and even some cleanup efforts. A classic indicator of compromise is a UID-0 account (other than `root`) in `/etc/passwd`, or an account with a suspiciously recent creation date and no legitimate business justification. Auditing `/etc/passwd` for duplicate UID 0 entries should be part of any host compromise investigation.

## Researcher's Note
Security researchers auditing a new system almost always run `awk -F: '$3 == 0 {print $1}' /etc/passwd` as one of their first commands — it lists every account with root-level UID 0. If more than one name appears, that's an immediate red flag worth investigating, since a legitimate system should only ever have `root` in that list.

## Real-World Example
In several documented breaches, attackers with temporary root access created a low-profile user account (sometimes disguised with a name resembling a system service, such as `sysupdate` or `httpd-helper`) and added it to the `sudo` or `wheel` group. Because the account looked unremarkable in a casual `ls /home` check, it went unnoticed for weeks, giving the attacker a durable way back into the system even after the original entry point was patched.

---

## Main Topic 1: The Identity Files

Linux stores account information in plain text files under `/etc/`. Understanding their structure is the single most useful skill in this lesson.

**`/etc/passwd`** — one line per user account:
```
username:x:UID:GID:comment:home_directory:shell
```
Example:
```
jsmith:x:1001:1001:John Smith:/home/jsmith:/bin/bash
```
The `x` in the second field means the password hash lives elsewhere (in `/etc/shadow`) — historically passwords were stored directly in `/etc/passwd`, which is why that file is world-readable and shadow passwords were introduced as a security improvement.

**`/etc/shadow`** — one line per user, storing password hashes and aging policy:
```
username:hashed_password:last_changed:min:max:warn:inactive:expire
```
This file is readable only by root (mode `600` or `640`), because it contains the actual password hashes.

**`/etc/group`** — one line per group:
```
group_name:x:GID:comma_separated_members
```

**`/etc/gshadow`** — the group equivalent of the shadow file, storing group passwords and administrators (rarely used today, but still present on most systems).

## Main Topic 2: Managing User Accounts

The core commands for user lifecycle management:

| Command | Purpose |
|---|---|
| `useradd` | Create a new user account |
| `usermod` | Modify an existing account |
| `userdel` | Delete an account |
| `passwd` | Set or change a password |
| `chsh` | Change a user's login shell |
| `id` | Show a user's UID, GID, and group memberships |
| `whoami` | Show the currently logged-in user |

Creating a user with a home directory and a specific shell:
```bash
sudo useradd -m -s /bin/bash jsmith
sudo passwd jsmith
```

Locking an account without deleting it (useful when offboarding, since it preserves file ownership history):
```bash
sudo usermod -L jsmith        # lock
sudo usermod -U jsmith        # unlock
```

Deleting a user and their home directory:
```bash
sudo userdel -r jsmith
```

Many distributions also ship `adduser`, a friendlier interactive wrapper around `useradd` — functionally similar but with guided prompts.

## Main Topic 3: Managing Groups and Membership

Groups let you grant a set of permissions once and apply them to many users. There are two kinds of membership:

- **Primary group** — set in `/etc/passwd`, this is the group assigned to any file the user creates.
- **Secondary (supplementary) groups** — additional groups listed in `/etc/group`, granting extra access (e.g., the `docker` or `sudo` group).

Core commands:
```bash
sudo groupadd developers                 # create a group
sudo usermod -aG developers jsmith       # add jsmith to it (ALWAYS use -aG, never -G alone)
groups jsmith                            # list jsmith's groups
sudo gpasswd -d jsmith developers        # remove jsmith from the group
sudo groupdel developers                 # delete a group
```

⚠️ The `-a` flag ("append") is critical. Running `usermod -G developers jsmith` *without* `-a` **replaces** all of jsmith's supplementary groups with just `developers` — a common and dangerous mistake that silently strips other access.

---

## Tips
- Use `id username` as your go-to command to see a full identity summary — UID, GID, and all group memberships at once.
- `getent passwd` and `getent group` read from whatever identity backend is configured (local files, LDAP, etc.), so they're more reliable than `cat /etc/passwd` on systems using centralized identity management.
- When in doubt about whether a change worked, re-run `id` — don't assume.

## Common Mistakes
- Forgetting `-a` with `usermod -G`, wiping out existing group memberships.
- Using `userdel` without `-r`, leaving an orphaned home directory (or the reverse — using `-r` when you actually needed to preserve files for evidence/legal hold).
- Editing `/etc/passwd` or `/etc/group` by hand instead of with `vipw`/`vigr`, risking file corruption that can lock out all logins.
- Assuming a locked account (`usermod -L`) is fully disabled — it only disables password login; SSH key-based login still works unless the account is also expired or the shell is set to `/usr/sbin/nologin`.

## Common Misconceptions
- **"Deleting a user removes all their files."** Not necessarily — `userdel` without `-r` leaves files owned by the now-orphaned UID scattered across the filesystem, which can itself be a security issue (a new user later created with the same UID inherits access to those files).
- **"Root and UID 0 are the same thing."** Root is the *name* conventionally tied to UID 0, but any account can be given UID 0, effectively making it a second root account — this is exactly the persistence technique described in the Cybersecurity Insight above.
- **"Groups only matter for file permissions."** Groups also gate access to devices, sudo privileges, Docker sockets, and many services — file permissions are just the most visible use case.

## Quick Practice
Run these on a lab system and predict the output before you hit enter:
```bash
id
cat /etc/passwd | grep $(whoami)
groups
```
Confirm: does your UID match what `id` reports? Is your primary group the same as your username (common on many distros) or something else?

## Knowledge Check
1. What is the difference between a primary and a secondary group?
2. Why is `/etc/shadow` not world-readable, unlike `/etc/passwd`?
3. What command would you use to add a user to a group *without* removing their existing group memberships?
4. Why is a second UID-0 account in `/etc/passwd` considered a red flag?
5. What's the difference between `usermod -L` and `userdel`?

<details>
<summary>Answers</summary>

1. The primary group is the default group assigned in `/etc/passwd` and applied to new files the user creates; secondary groups are additional memberships listed in `/etc/group` for extra access.
2. Because it stores password hashes; if it were world-readable, any local user could attempt offline password cracking against every account.
3. `sudo usermod -aG groupname username` — the `-a` flag preserves existing memberships.
4. UID 0 grants full root privileges; a legitimate system should have exactly one such account (`root`), so a second one often indicates a backdoor.
5. `usermod -L` locks password login but leaves the account and its files intact; `userdel` removes the account entirely (and optionally its home directory with `-r`).
</details>

## Best Practices
- Use `-aG`, never bare `-G`, when adding supplementary groups.
- Lock or expire accounts (`usermod -L`, `chage -E`) rather than immediately deleting them during offboarding, to preserve an audit trail.
- Regularly audit `/etc/passwd` for UID 0 duplicates and unexpected accounts as part of routine host hardening.
- Use dedicated service accounts (with `/usr/sbin/nologin` as the shell) for applications instead of running them as your personal or root account.
- Enforce password aging policy with `chage` for any account that uses password authentication.

## Summary
User and group management is the identity foundation of Linux security. `/etc/passwd`, `/etc/shadow`, and `/etc/group` define who exists on a system and what they belong to; `useradd`, `usermod`, `groupadd`, and their companions let you manage that lifecycle safely. A careful administrator — and a sharp-eyed investigator — always knows how to read these files and knows what "normal" looks like, so anomalies stand out immediately.

## What's Next?
Now that you understand *who* can access a system, Lesson 02 covers *what* they're allowed to do with files once they're identified — the Linux file permission model.

---

## Navigation
⬅️ [Back to Module README](README.md) | ➡️ [Next: Lesson 02 – File Permissions](02-file-permissions.md)
