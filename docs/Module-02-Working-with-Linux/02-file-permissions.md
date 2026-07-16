# 📖 Lesson 02 – File Permissions

## Learning Outcomes
By the end of this lesson, you will be able to:
- Read and interpret Linux permission strings (`rwxr-xr-x`) and octal notation (`755`)
- Modify permissions and ownership with `chmod`, `chown`, and `chgrp`
- Explain and apply special permission bits: SUID, SGID, and the sticky bit
- Identify permission misconfigurations that create privilege escalation opportunities

## Estimated Study Time
60 minutes (including lab)

## Difficulty
🟡 Beginner–Intermediate

## Prerequisites
- Lesson 01: Users and Groups
- Comfortable navigating directories with `cd`, `ls`, `pwd`

## Key Terms
| Term | Definition |
|---|---|
| **Permission bit** | A flag indicating read (r), write (w), or execute (x) access |
| **Octal notation** | Permissions expressed as a 3-digit number (e.g., `644`) |
| **SUID** | Set User ID — a file executes with the *owner's* privileges, not the caller's |
| **SGID** | Set Group ID — a file executes with the *group's* privileges, or new files in a directory inherit its group |
| **Sticky bit** | On a directory, restricts deletion of files to their owner (even if others have write access) |
| **umask** | A mask that determines default permissions for newly created files/directories |
| **ACL** | Access Control List — a more granular permission model beyond owner/group/other |

## Introduction
If users and groups answer "who," file permissions answer "what are they allowed to do." Linux's permission model is deceptively simple on the surface — three permission types (read, write, execute) applied to three actor categories (owner, group, other) — but it underlies nearly every access-control decision on the system, and its edge cases (special bits, inherited permissions, ACLs) are where a huge fraction of real-world privilege escalation vulnerabilities live.

## Why This Matters
Misconfigured file permissions are one of the most common — and most exploitable — security weaknesses on Linux systems. A world-writable configuration file, a SUID binary that shouldn't have that bit set, or an overly permissive script run by a cron job as root can all be direct paths to full system compromise. Every penetration test and every hardening review includes a permissions audit.

## Cybersecurity Insight
`find / -perm -4000 -type f 2>/dev/null` is one of the first commands run in almost every Linux privilege escalation assessment — it lists every SUID binary on the system. If any unexpected or custom binary appears in that list (rather than the standard set like `passwd`, `sudo`, `su`), it's worth investigating immediately, because a SUID binary owned by root that has any way to execute arbitrary commands is a direct route to root access.

## Researcher's Note
GTFOBins (gtfobins.github.io) is a well-known researcher-maintained catalog of standard Unix binaries that can be abused to bypass local security restrictions when they carry excessive permissions (like SUID) or overly broad sudo rules. Security researchers cross-reference a system's SUID binary list against GTFOBins as a standard escalation-path check.

## Real-World Example
A common real-world finding in penetration tests: a backup script left with `777` permissions in a world-writable directory, executed periodically by root via cron. Any local user can edit that script to include arbitrary commands, which then execute with root privileges the next time cron runs it — turning a low-privilege foothold into full root access without exploiting any software vulnerability at all, just a permissions mistake.

---

## Main Topic 1: Reading and Understanding Permissions

Running `ls -l` shows a permission string like this:
```
-rwxr-xr-x  1 jsmith developers  8192 Jul 16 09:00 deploy.sh
```
Breaking down `-rwxr-xr-x`:
- Position 1: file type (`-` = regular file, `d` = directory, `l` = symlink)
- Positions 2–4: owner permissions (`rwx`)
- Positions 5–7: group permissions (`r-x`)
- Positions 8–10: other/world permissions (`r-x`)

Each triad is read/write/execute. For a **file**, execute means "can run this as a program/script." For a **directory**, execute means "can enter/traverse this directory" — a subtlety that trips up almost everyone at first: a directory can be readable (you can `ls` it) but not executable (you can't `cd` into it or access files inside, even ones you own).

## Main Topic 2: Changing Permissions and Ownership

**Octal notation** assigns each permission a value: read = 4, write = 2, execute = 1. Sum them per actor:
```
rwx = 7    rw- = 6    r-x = 5    r-- = 4    --- = 0
```
So `755` means owner=rwx, group=r-x, other=r-x — a very common script permission.

```bash
chmod 755 deploy.sh          # octal form
chmod u+x deploy.sh          # symbolic form: add execute for owner
chmod go-w file.txt          # remove write from group and other
chmod -R 750 /var/www/app    # recursive
```

Ownership is changed with `chown` and `chgrp`:
```bash
sudo chown jsmith deploy.sh              # change owner
sudo chown jsmith:developers deploy.sh   # change owner and group
sudo chgrp developers deploy.sh          # change group only
sudo chown -R www-data:www-data /var/www # recursive ownership change
```

## Main Topic 3: Special Permissions and umask

Beyond the standard `rwx`, three special bits change how a file or directory behaves:

**SUID (`4000`)** — an executable runs with the privileges of its *owner*, regardless of who runs it. Classic example: `/usr/bin/passwd` is owned by root and has SUID set, so any user can change their password (which requires writing to root-owned `/etc/shadow`) without being root themselves.
```bash
chmod u+s /path/to/binary     # symbolic
chmod 4755 /path/to/binary    # octal (leading 4)
```

**SGID (`2000`)** — on an executable, it runs with the *group's* privileges; on a **directory**, any new file created inside inherits the directory's group instead of the creator's primary group — very useful for shared team directories.
```bash
chmod g+s /shared/project-dir
chmod 2775 /shared/project-dir
```

**Sticky bit (`1000`)** — on a directory, only the file's owner (or root) can delete or rename it, even if others have write access to the directory. `/tmp` is the textbook example — everyone can write there, but you can't delete someone else's files.
```bash
chmod +t /shared/dropbox
chmod 1777 /shared/dropbox
```

**umask** controls default permissions on file creation. A umask of `022` subtracts write access for group and other from the default (666 for files, 777 for directories), producing `644` files and `755` directories — the common secure default.
```bash
umask          # show current mask
umask 027      # set a stricter mask for the session
```

---

## Tips
- Use `ls -ld directoryname` (note the `-d`) to see permissions *of* a directory rather than its contents.
- `stat filename` gives a more detailed permission and ownership breakdown than `ls -l`, including octal notation directly.
- When unsure what octal value to use, build it triad by triad: decide owner, group, other separately, then combine.

## Common Mistakes
- Running `chmod 777` "just to make it work" — this is almost never the correct fix and often introduces a security hole; the real fix is usually an ownership problem, not a permission problem.
- Forgetting that directory execute permission is required to traverse it, and mistaking "permission denied" on files inside a directory for a file-level problem when it's actually the directory.
- Using `chown -R` carelessly on a path that includes files you didn't intend to affect (always double-check the path first).
- Confusing SUID/SGID on files (privilege execution) with SGID on directories (group inheritance) — they behave very differently depending on file type.

## Common Misconceptions
- **"root can always read every file."** Mostly true, but not absolute — mandatory access control systems like SELinux or AppArmor can restrict even root, and some filesystems support immutable attributes (`chattr +i`) that block modification regardless of permission bits.
- **"A `644` file is always safe."** Safety depends on *content*, not just permission bits — a `644` file containing a plaintext password is still a problem even though the permission itself looks conservative.
- **"SUID is inherently dangerous and should never be used."** SUID is a legitimate and necessary mechanism (e.g., `passwd`, `ping` historically) — the danger is *unnecessary or unaudited* SUID bits, not the mechanism itself.

## Quick Practice
```bash
touch testfile
chmod 640 testfile
ls -l testfile
stat testfile
```
Predict the `ls -l` output before running it, then confirm the octal value shown by `stat` matches your `chmod` command.

## Knowledge Check
1. What does execute permission mean on a directory versus on a file?
2. Convert `rwxr-x---` to octal notation.
3. What is the security purpose of the sticky bit on `/tmp`?
4. Why is SUID considered higher-risk than SGID in most privilege escalation scenarios?
5. What command finds all SUID binaries on a system?

<details>
<summary>Answers</summary>

1. On a directory, execute allows traversal (entering/accessing contents); on a file, it allows execution as a program/script.
2. `750`
3. It prevents users from deleting or renaming files they don't own inside a shared, world-writable directory.
4. SUID files run as the *owner*, which is frequently root, granting full root privileges if exploited; SGID typically grants only group-level privileges, which is usually a smaller blast radius.
5. `find / -perm -4000 -type f 2>/dev/null`
</details>

## Best Practices
- Default to the least-permissive setting that still allows the system to function; add permissions incrementally rather than starting from `777`.
- Regularly audit SUID/SGID binaries against a known-good baseline.
- Use group ownership and SGID directories for team-shared paths instead of loosening "other" permissions.
- Prefer ACLs (`setfacl`/`getfacl`) over broad permission bits when you need fine-grained, multi-user access control.
- Set a sane `umask` (typically `022` or `027`) at the system or shell profile level rather than relying on manual `chmod` after every file creation.

## Summary
File permissions are Linux's core access-control mechanism, expressed through owner/group/other read-write-execute bits and extended by special SUID, SGID, and sticky bits. Reading and setting these correctly — and knowing where to look for anomalies like unexpected SUID binaries — is one of the highest-value skills for both hardening systems and assessing them for weaknesses.

## What's Next?
With identity and access control covered, Lesson 03 moves to what's actually running on the system right now: processes, and how to inspect and control them.

---

## Navigation
⬅️ [Previous: Lesson 01 – Users and Groups](01-users-and-groups.md) | 🏠 [Module README](README.md) | ➡️ [Next: Lesson 03 – Processes](03-processes.md)
