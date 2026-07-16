# 📖 Lesson 05 – Package Managers

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain the role of a package manager and package repositories
- Install, update, and remove software using `apt` and `dnf`/`yum`
- Query installed packages and verify package integrity
- Audit installed software for outdated or vulnerable packages
- Understand the security risks of untrusted repositories and packages

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟢 Beginner–Intermediate

## Prerequisites
- Basic terminal comfort
- Lesson 01 helpful (package managers often need `sudo`, tying back to privilege)

## Key Terms
| Term | Definition |
|---|---|
| **Package** | A bundled, versioned unit of software plus metadata (dependencies, install scripts) |
| **Repository (repo)** | A curated server hosting packages that a package manager can install from |
| **Dependency** | Another package required for a given package to function |
| **Package manager** | The tool that resolves, downloads, installs, updates, and removes packages (`apt`, `dnf`, `yum`, `pacman`, etc.) |
| **GPG signing** | Cryptographic signing used to verify a package hasn't been tampered with |
| **Metapackage** | A package with no real content, existing only to pull in a set of dependencies |

## Introduction
Almost nothing on a modern Linux system is installed by manually downloading and compiling source code — software arrives through **package managers**, which handle downloading, dependency resolution, installation, updates, and removal in a consistent, trackable way. Debian-based distributions (Debian, Ubuntu) use `apt`/`dpkg`; Red Hat-based distributions (RHEL, Fedora, CentOS, Rocky, Alma) use `dnf`/`yum`/`rpm`. Understanding these tools is essential for both routine administration and for auditing what software — and what vulnerabilities — actually exist on a system.

## Why This Matters
Outdated or unpatched software is one of the single most common root causes of successful attacks — vulnerability scanners and threat actors alike specifically look for known-vulnerable package versions. Being fluent with package managers means you can quickly answer "is this system patched?", "what installed this?", and "can I trust where this software came from?" — questions at the heart of vulnerability management and supply chain security.

## Cybersecurity Insight
Adding an untrusted third-party repository (or disabling GPG signature verification "just to get something installed") is a well-documented supply chain risk — it hands the repository operator the ability to push arbitrary code to your system with root privileges the next time you run an update. Reviewing `/etc/apt/sources.list` and `/etc/apt/sources.list.d/` (Debian/Ubuntu) or `/etc/yum.repos.d/` (RHEL/Fedora) for unexpected or unfamiliar repositories is a standard part of a host security audit.

## Researcher's Note
Vulnerability researchers and defenders commonly cross-reference the output of `dpkg -l` or `rpm -qa` (a full list of installed packages and versions) against CVE databases to identify known-vulnerable software on a host — this is essentially what automated vulnerability scanners do under the hood, and it's a useful manual skill for quickly triaging a system without deploying a full scanner.

## Real-World Example
Multiple widely reported breaches trace back to internet-facing services running an outdated package version with a publicly known, already-patched CVE — the fix existed, but the update was never applied. This pattern is common enough that "patch management" is consistently cited as one of the highest-return, lowest-glamour security investments an organization can make, precisely because package managers make patching straightforward when it's prioritized.

---

## Debian-Based Systems — apt and dpkg

`apt` (and its lower-level counterpart `dpkg`) is used on Debian, Ubuntu, and derivatives.

```bash
sudo apt update                     # refresh the local package index (does NOT install anything)
sudo apt upgrade                    # upgrade all installed packages to latest available
sudo apt install nginx              # install a package
sudo apt remove nginx               # remove a package, keep config files
sudo apt purge nginx                # remove a package AND its config files
sudo apt autoremove                 # remove packages installed as dependencies no longer needed
apt list --installed                # list all installed packages
apt search keyword                  # search available packages
apt show nginx                      # show detailed package info
```
`dpkg` operates on local `.deb` files directly and is useful for lower-level queries:
```bash
dpkg -l                    # list all installed packages
dpkg -l | grep nginx       # check if a specific package is installed
dpkg -L nginx              # list every file installed by a package
dpkg -S /usr/sbin/nginx    # find which package owns a given file
```
`apt update` is easily confused with `apt upgrade` — `update` only refreshes the *index* of what's available; it installs nothing by itself. This is one of the most common points of confusion for people new to Debian-based systems.

## Red Hat-Based Systems — dnf/yum and rpm

`dnf` (modern) and `yum` (older, still common on RHEL 7/CentOS 7) are used on RHEL, Fedora, Rocky Linux, AlmaLinux, and derivatives. Command syntax is very close between the two.

```bash
sudo dnf check-update              # see what updates are available (like apt update, informational)
sudo dnf upgrade                   # upgrade all installed packages
sudo dnf install httpd             # install a package
sudo dnf remove httpd              # remove a package
dnf list installed                 # list installed packages
dnf search keyword                 # search available packages
dnf info httpd                     # detailed package info
```
`rpm` is the lower-level tool, analogous to `dpkg`:
```bash
rpm -qa                     # list all installed packages
rpm -qa | grep httpd        # check if a specific package is installed
rpm -ql httpd                # list files installed by a package
rpm -qf /usr/sbin/httpd      # find which package owns a given file
rpm -qi httpd                # detailed package info for an installed package
```

## Repositories, Verification, and Auditing

Package sources are configured in text files:
- Debian/Ubuntu: `/etc/apt/sources.list` and `/etc/apt/sources.list.d/*.list`
- RHEL/Fedora: `/etc/yum.repos.d/*.repo`

Every legitimate repository signs its packages with GPG so the package manager can verify they haven't been tampered with in transit. Never run commands that disable signature checks (`--allow-unauthenticated` in apt, `--nogpgcheck` in dnf/rpm) outside of a deliberate, well-understood testing scenario — doing so removes your primary defense against a compromised or malicious mirror serving tampered packages.

**Auditing installed software** for security purposes typically involves:
```bash
apt list --upgradable                        # Debian/Ubuntu: what's out of date
dnf check-update                               # RHEL/Fedora: what's out of date
dpkg -l | wc -l                                # quick count of installed packages
grep -r "^deb " /etc/apt/sources.list*         # review configured repositories (Debian/Ubuntu)
ls /etc/yum.repos.d/                           # review configured repositories (RHEL/Fedora)
```
Dedicated vulnerability scanners (e.g., distro-provided tools, or third-party scanners) go further by cross-referencing installed versions against CVE databases automatically, but the manual commands above are the foundation those tools build on.

---

## Tips
- `apt update` first, always, before `apt install` or `apt upgrade` — an outdated index can cause "package not found" errors for software that does exist.
- Use `apt-cache policy <package>` (Debian/Ubuntu) or `dnf info <package>` to see exactly which repository a package would be installed from before you install it.
- Keep a habit of running updates on a regular cadence rather than only when something breaks — patch lag is a measurable, well-known risk factor.

## Common Mistakes
- Confusing `apt update` (refresh index) with `apt upgrade` (actually install newer versions).
- Adding a third-party repository without reviewing what it is or who maintains it, then forgetting it's there.
- Using `apt remove` when `apt purge` was intended (or vice versa), leaving stale config files around or unexpectedly deleting configuration you wanted to keep.
- Disabling GPG verification to "fix" an installation error instead of investigating why verification is failing.

## Common Misconceptions
- **"If it's in the official repository, it's automatically safe forever."** Official repositories are far more trustworthy than random third-party sources, but packages can still contain vulnerabilities that are discovered later — repository trust reduces risk, it doesn't eliminate the need to patch.
- **"Removing a package fully undoes its installation footprint."** Configuration files, log files, and data directories often persist after `remove` (by design) and require `purge` (Debian/Ubuntu) or manual cleanup to fully remove.
- **"apt and yum/dnf are interchangeable across distributions."** They serve the same conceptual role but use different package formats (`.deb` vs `.rpm`) and are not cross-compatible — a `.deb` package cannot be installed with `dnf`.

## Quick Practice
```bash
# Debian/Ubuntu
apt list --upgradable
# RHEL/Fedora
dnf check-update
```
Run whichever matches your lab distribution and note how many packages have available updates.

## Knowledge Check
1. What's the difference between `apt update` and `apt upgrade`?
2. What's the difference between `apt remove` and `apt purge`?
3. Why is disabling GPG signature verification considered a security risk?
4. What command would tell you which installed package owns a specific file on disk?
5. Why is patch management consistently cited as high-value from a security perspective?

<details>
<summary>Answers</summary>

1. `update` refreshes the local index of available packages/versions without installing anything; `upgrade` actually installs newer versions of already-installed packages.
2. `remove` uninstalls the package but leaves configuration files behind; `purge` removes the package and its configuration files.
3. GPG signatures verify a package hasn't been tampered with in transit or by a compromised mirror; disabling that check means you're trusting the download's integrity with no verification.
4. `dpkg -S /path/to/file` (Debian/Ubuntu) or `rpm -qf /path/to/file` (RHEL/Fedora).
5. Many successful attacks exploit already-known, already-patched vulnerabilities in outdated software — keeping systems updated closes off a large, well-understood category of risk relatively cheaply compared to more exotic defenses.
</details>

## Best Practices
- Run `apt update`/`dnf check-update` regularly, and apply upgrades on a defined schedule rather than ad hoc.
- Review configured repositories periodically; remove any you don't recognize or no longer need.
- Never disable GPG/signature verification outside of a controlled, well-understood testing context.
- Use `purge` (not just `remove`) when you want a package's configuration fully gone, particularly for security-sensitive software.
- Maintain an inventory of installed packages on critical systems so unexpected additions stand out during an audit.

## Summary
Package managers are how software — and therefore both functionality and vulnerabilities — arrive on a Linux system. `apt`/`dpkg` on Debian-based systems and `dnf`/`yum`/`rpm` on Red Hat-based systems provide install, update, removal, and query capabilities that are foundational to both administration and security auditing. Trusting only signed, well-maintained repositories and keeping systems patched are two of the highest-leverage security practices available.

## What's Next?
Software doesn't just depend on packages — it also depends on configuration passed through the environment it runs in. Lesson 06 covers environment variables: how programs read configuration and secrets from the shell around them.

---

## Navigation
⬅️ [Previous: Lesson 04 – Services](04-services.md) | [Lab 05 : package managers](../../labs/module-02/05-package-managers.md) | ➡️ [Next: Lesson 06 – Environment Variables](06-environment-variables.md)
