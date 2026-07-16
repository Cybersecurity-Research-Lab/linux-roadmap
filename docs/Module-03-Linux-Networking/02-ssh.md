# 📖 Lesson 02 – SSH

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain how SSH provides encrypted, authenticated remote access
- Connect to remote hosts and copy files securely using SSH tooling
- Generate, manage, and deploy SSH key pairs for authentication
- Harden an SSH server configuration against common attacks
- Recognize common SSH-related attack patterns and misconfigurations

## Estimated Study Time
55 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 01: Linux Networking Basics
- Lesson 02 from the Linux Fundamentals module (File Permissions) is helpful, since SSH key security depends heavily on correct file permissions

## Key Terms
| Term | Definition |
|---|---|
| **SSH** | Secure Shell — an encrypted protocol for remote login and command execution |
| **Key pair** | A public/private cryptographic key pair used for SSH authentication |
| **`authorized_keys`** | A file listing public keys permitted to log in as a given user |
| **Host key** | A key pair identifying the SSH *server* itself, used to detect impersonation |
| **Port forwarding / tunneling** | Using an SSH connection to relay traffic for another service |
| **Brute force** | Repeated automated login attempts trying many password/key combinations |
| **Fail2ban** | A tool that monitors logs and temporarily bans IPs showing attack patterns (e.g., repeated failed SSH logins) |

## Introduction
SSH (Secure Shell) is the standard way Linux systems are administered remotely, replacing older, unencrypted protocols like Telnet and rlogin. It provides an encrypted channel for interactive shells, command execution, and file transfer, along with strong authentication options. Because SSH is almost universally exposed on Linux servers, it's also one of the most consistently targeted services on the internet — understanding it well is essential both for legitimate administration and for security hardening.

## Why This Matters
SSH is simultaneously the most common legitimate remote access method for Linux and one of the most heavily attacked services on the public internet — automated brute-force scanning against port 22 is constant background noise on any internet-facing host. Misconfigured SSH (weak passwords allowed, root login enabled, outdated protocol settings) is a direct and frequently exploited path to full system compromise, making SSH hardening one of the highest-value, most universally applicable security practices in this entire module.

## Cybersecurity Insight
Disabling password authentication entirely and requiring key-based authentication is one of the single most effective SSH hardening steps available — it makes online brute-force attacks against SSH essentially useless, since there's no password to guess. Combined with disabling direct root login (`PermitRootLogin no`) and changing or firewall-restricting the default port, these three settings address the overwhelming majority of automated SSH attack traffic seen against real internet-facing hosts.

## Researcher's Note
Security researchers and honeypot operators who expose SSH on the open internet consistently observe massive volumes of automated login attempts within minutes of a host going live, typically cycling through common usernames (`root`, `admin`, `ubuntu`) and password lists. This observed pattern is why `journalctl -u ssh` or `/var/log/auth.log` review, combined with tools like `fail2ban`, is considered baseline hygiene rather than an advanced defensive measure for any SSH-exposed host.

## Real-World Example
Numerous publicly documented breaches trace back to SSH access gained through weak or reused passwords, particularly on cloud instances left with default or weak credentials and password authentication still enabled. Conversely, security guidance from cloud providers and CIS benchmarks alike consistently lists "disable SSH password authentication" and "disable root SSH login" among the very first hardening steps recommended for any new Linux server, reflecting how disproportionately effective these two changes are relative to their implementation cost.

---

## Connecting and Basic SSH Usage

```bash
ssh user@remotehost                     # connect
ssh -p 2222 user@remotehost               # connect on a non-default port
ssh -i ~/.ssh/mykey user@remotehost          # specify a private key explicitly
ssh user@remotehost "command"                  # run a single remote command and exit
```

File transfer over SSH:
```bash
scp file.txt user@remotehost:/remote/path/         # copy a local file to remote
scp user@remotehost:/remote/file.txt ./              # copy a remote file to local
scp -r localdir/ user@remotehost:/remote/path/          # recursive, for directories
rsync -avz localdir/ user@remotehost:/remote/path/         # more efficient, incremental sync
```
`rsync` over SSH is generally preferred over `scp` for anything beyond a quick one-off copy, since it only transfers changed data and supports resuming interrupted transfers.

## SSH Key-Based Authentication

Generating a key pair:
```bash
ssh-keygen -t ed25519 -C "you@example.com"        # modern, recommended algorithm
ssh-keygen -t rsa -b 4096 -C "you@example.com"      # widely compatible alternative
```
This produces two files: a **private key** (e.g., `id_ed25519` — never share this, ever) and a **public key** (`id_ed25519.pub` — safe to share and distribute).

Deploying your public key to a remote server:
```bash
ssh-copy-id user@remotehost                # easiest method, appends your key automatically
# manual equivalent:
cat ~/.ssh/id_ed25519.pub | ssh user@remotehost "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```
Correct permissions are critical — SSH will silently refuse to use `authorized_keys` (or the private key) if permissions are too permissive:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519          # private key: owner read/write only
chmod 644 ~/.ssh/id_ed25519.pub       # public key: fine to be more open
```
This directly connects back to Lesson 02 of the Linux Fundamentals module (File Permissions) — SSH's refusal to use over-permissive key files is a deliberate security control, not a bug.

## Hardening the SSH Server

The server configuration lives at `/etc/ssh/sshd_config`. Key hardening settings:
```
PermitRootLogin no              # disable direct root login over SSH
PasswordAuthentication no        # require key-based auth only
PubkeyAuthentication yes           # ensure key auth is enabled
Port 2222                            # optional: move off the default port to reduce noise
MaxAuthTries 3                         # limit login attempts per connection
AllowUsers alice bob                     # explicit allowlist of who may log in via SSH
X11Forwarding no                           # disable unless genuinely needed
```
After editing, always validate syntax before restarting, since a broken config can lock out all SSH access:
```bash
sudo sshd -t                        # test configuration syntax
sudo systemctl restart sshd            # apply changes
```
⚠️ **Never disconnect your only SSH session immediately after restarting `sshd`** — keep your current session open and test a *new* connection in a second window first, so you can revert quickly if something is broken.

`fail2ban` adds automated protection against brute-force attempts by watching auth logs and temporarily banning offending IPs:
```bash
sudo apt install fail2ban          # Debian/Ubuntu
sudo systemctl enable --now fail2ban
fail2ban-client status sshd           # check current ban status
```

---

## Tips
- Use `ssh -v` (or `-vvv` for maximum detail) when troubleshooting connection failures — it shows exactly which authentication methods were tried and why each failed.
- Keep a `~/.ssh/config` file to avoid retyping long connection strings:
  ```
  Host myserver
      HostName 203.0.113.10
      User alice
      Port 2222
      IdentityFile ~/.ssh/id_ed25519
  ```
  Then simply `ssh myserver`.
- Rotate and retire SSH keys periodically, and always remove a departing team member's public key from every `authorized_keys` file they had access to.

## Common Mistakes
- Setting overly permissive permissions on `~/.ssh` or private key files, causing SSH to silently reject them.
- Restarting `sshd` after a config change without testing syntax first (`sshd -t`), risking a total lockout.
- Leaving `PermitRootLogin` and `PasswordAuthentication` at their defaults on an internet-facing server.
- Sharing a private key file (instead of the public key) when setting up access for someone else — the private key must never leave the machine it was generated on (or a secure, deliberate key management process).

## Common Misconceptions
- **"Changing the SSH port makes the server secure."** Moving off port 22 reduces automated scanning *noise* but is not a real security control by itself — it's "security through obscurity" and should be layered on top of, never substituted for, key-based auth and proper access controls.
- **"SSH keys don't need a passphrase since the file permissions already protect them."** File permissions protect against other *local* users on the same machine; a passphrase protects the key if the file itself is ever copied or stolen, which permissions alone can't prevent.
- **"Once `PasswordAuthentication no` is set, brute-force attempts stop appearing in logs."** Attackers will still attempt password logins and get rejected — the log noise continues, but the attacks now fail immediately rather than being able to succeed against a weak password.

## Quick Practice
```bash
ssh-keygen -t ed25519 -C "lab-test"
ls -l ~/.ssh/
```
Confirm the private key shows `600` permissions and the public key is readable; identify which file you would (and would never) share with a remote server administrator.

## Knowledge Check
1. Why is disabling password authentication considered one of the highest-value SSH hardening steps?
2. What happens if `~/.ssh/authorized_keys` has overly permissive file permissions?
3. Why should you always test `sshd -t` before restarting the SSH service after a configuration change?
4. What is the difference between a private key and a public key in the context of SSH authentication?
5. Why is changing the default SSH port considered "security through obscurity" rather than a real security control?

<details>
<summary>Answers</summary>

1. It eliminates the possibility of a successful password-guessing/brute-force attack entirely, since there's no password to guess against.
2. SSH will silently refuse to use it for authentication, as a built-in security measure against key files that could have been tampered with by other local users.
3. A syntax error in the config could prevent `sshd` from starting or accepting connections at all, potentially locking out all remote access until fixed via console/physical access.
4. The private key must be kept secret and never shared, used to prove identity; the public key is safe to distribute and is what gets placed in a server's `authorized_keys` file to permit that identity to log in.
5. It only reduces automated scanning noise on the default port; a targeted attacker can still find the actual port via a port scan, so it provides no protection against a determined attacker and shouldn't replace real authentication controls.
</details>

## Best Practices
- Disable password authentication and require key-based auth wherever operationally feasible.
- Disable direct root login (`PermitRootLogin no`) and require administrators to authenticate as themselves, then `sudo`.
- Use `AllowUsers`/`AllowGroups` to explicitly limit who may connect via SSH.
- Deploy `fail2ban` or an equivalent to automatically respond to brute-force attempts.
- Always validate config syntax (`sshd -t`) and keep an existing session open before restarting `sshd` after changes.
- Protect private keys with a passphrase, especially on laptops or any device that could be lost or stolen.

## Summary
SSH provides encrypted, authenticated remote access and underpins nearly all real-world Linux administration — which also makes it one of the most consistently attacked services on the internet. Key-based authentication, disabling root login, and layered protections like `fail2ban` address the overwhelming majority of real-world SSH attack traffic, making SSH hardening one of the highest-return security investments covered in this module.

## What's Next?
With secure remote access covered, Lesson 03 introduces the broader toolkit of network utilities used for diagnostics, monitoring, and reconnaissance — many of which you'll use directly through the SSH sessions this lesson taught you to establish.


---

## Navigation
⬅️ [Previous: Lesson 01 – Linux Networking Basics](01-linux-networking-basics.md) | [Lab 02 : ssh](../../labs/module-03/02-ssh.md) | ➡️ [Next: Lesson 03 – Network Utilities](03-network-utilities.md)
