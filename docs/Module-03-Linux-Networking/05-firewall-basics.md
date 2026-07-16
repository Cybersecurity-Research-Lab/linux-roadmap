# 📖 Lesson 05 – Firewall Basics

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain how Linux host-based firewalls filter traffic using the netfilter framework
- Configure basic firewall rules using `ufw`, `firewalld`, and `nftables`/`iptables`
- Design a default-deny firewall policy with targeted allow rules
- Verify and troubleshoot firewall rule behavior
- Recognize common firewall misconfigurations and their security impact

## Estimated Study Time
60 minutes (including lab)

## Difficulty
🔴 Intermediate–Advanced

## Prerequisites
- Lesson 01: Linux Networking Basics
- Lesson 02: SSH (firewall misconfiguration can lock out SSH access — critical to understand first)
- Lesson 03: Network Utilities (used throughout this lesson for verification)

## Key Terms
| Term | Definition |
|---|---|
| **Firewall** | A system that filters network traffic based on defined rules |
| **netfilter** | The Linux kernel framework that firewalls like iptables/nftables are built on |
| **Default-deny** | A policy where all traffic is blocked unless explicitly allowed |
| **Default-allow** | A policy where all traffic is permitted unless explicitly blocked |
| **Stateful firewall** | A firewall that tracks connection state, allowing return traffic for established connections automatically |
| **Chain** | An ordered sequence of firewall rules evaluated against matching traffic |
| **Zone** | (firewalld concept) A named set of trust levels/rules applied to specific interfaces |

## Introduction
A firewall is the mechanism that actually enforces what network traffic is allowed to reach a Linux host, and what that host is allowed to send out — everything from the interfaces and ports discussed in Lesson 01 to the services exposed via SSH becomes concretely controllable through firewall rules. Linux firewalling is built on the kernel's **netfilter** framework, accessible through several different tools with different levels of abstraction: the low-level `iptables`/`nftables`, and the more user-friendly `ufw` (Ubuntu/Debian-focused) and `firewalld` (RHEL/Fedora-focused, zone-based).

## Why This Matters
A correctly configured firewall is one of the most fundamental security controls on any Linux host — it's the last line of defense that determines whether an exposed, vulnerable service is actually reachable by an attacker in the first place. Conversely, a misconfigured firewall (rules in the wrong order, an overly broad allow rule, or a policy that gets accidentally reset) can either expose a system that was assumed to be protected, or lock out legitimate access including your own remote administration.

## Cybersecurity Insight
A **default-deny** policy (block everything, then explicitly allow only what's needed) is dramatically more secure than a **default-allow** policy (allow everything, then block specific known-bad things) — default-deny fails safe (an unexpected new service is blocked by default until deliberately opened), while default-allow fails open (any new service is exposed by default unless someone remembers to block it). This single design choice is one of the most consistently repeated recommendations across virtually every security hardening framework and benchmark.

## Researcher's Note
Security researchers auditing a host's firewall configuration typically start by listing the active rule set and policy defaults (`iptables -L -n -v` or `nft list ruleset`), specifically checking whether the default policy is `DROP`/`REJECT` (default-deny) or `ACCEPT` (default-allow), and then reviewing whether the specific allow rules are as narrow as operationally possible (specific ports, specific source IP ranges where feasible) rather than broad catch-all allows.

## Real-World Example
A commonly cited category of cloud security incident involves administrators temporarily opening a broad firewall rule (e.g., allowing all traffic from `0.0.0.0/0` — meaning literally anywhere on the internet — to a management port) to solve an immediate connectivity problem during setup or troubleshooting, then never narrowing it back down. Cloud security posture research has repeatedly found this exact pattern — overly broad security group/firewall rules left in place indefinitely — among the most common misconfigurations discovered during external security assessments of cloud infrastructure.

---

##  ufw — Uncomplicated Firewall (Debian/Ubuntu)

`ufw` provides a simplified interface over `iptables`, designed for common cases without needing to write raw netfilter rules directly.

```bash
sudo ufw status verbose              # check current status and rules
sudo ufw default deny incoming         # default-deny for inbound (the recommended baseline)
sudo ufw default allow outgoing          # typically fine to allow outbound by default
sudo ufw allow 22/tcp                       # allow SSH — DO THIS BEFORE ENABLING if connected via SSH
sudo ufw allow 80/tcp                          # allow HTTP
sudo ufw allow from 192.168.1.0/24 to any port 5432   # allow Postgres only from a specific subnet
sudo ufw deny 23                                         # explicitly deny Telnet
sudo ufw enable                                             # activate the firewall
sudo ufw delete allow 80/tcp                                   # remove a specific rule
```
⚠️ **Always allow SSH explicitly before running `ufw enable`** if you're connected remotely — enabling a default-deny firewall without an SSH allow rule already in place will immediately disconnect you with no way back in except console/physical access.

##  firewalld (RHEL/Fedora) and the Zone Model

`firewalld` uses a **zone**-based model — each network interface is assigned to a zone (e.g., `public`, `internal`, `trusted`), and rules are applied per zone rather than globally.

```bash
sudo firewall-cmd --get-active-zones           # which zones are in use, and which interfaces
sudo firewall-cmd --zone=public --list-all       # rules for a specific zone
sudo firewall-cmd --zone=public --add-service=ssh --permanent    # allow SSH persistently
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent    # allow a custom port persistently
sudo firewall-cmd --reload                                            # apply permanent changes
sudo firewall-cmd --zone=public --remove-service=ssh --permanent         # remove a rule
```
The `--permanent` flag is critical — without it, changes apply immediately but are lost on reload/reboot; with it, changes are saved but don't take effect until `--reload` (or you can combine testing a rule live first, then making it permanent once confirmed).

## iptables/nftables — The Underlying Framework

`nftables` is the modern replacement for `iptables`, though `iptables` syntax remains extremely common in existing documentation, scripts, and systems (often implemented as a compatibility layer over nftables on current distributions).

Basic `iptables` example building a default-deny policy:
```bash
sudo iptables -L -n -v                          # list current rules
sudo iptables -P INPUT DROP                        # default-deny inbound
sudo iptables -P FORWARD DROP                         # default-deny forwarded traffic
sudo iptables -A INPUT -i lo -j ACCEPT                   # always allow loopback traffic
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT   # allow return traffic for existing connections
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT          # allow SSH
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT            # allow HTTPS
```
Equivalent concept in `nft`:
```bash
sudo nft list ruleset
sudo nft add rule inet filter input tcp dport 22 accept
```
**Rule order matters** — chains are evaluated top to bottom, and the first matching rule wins (for `iptables`; `nftables` has more flexible verdict semantics but ordering within a chain still matters). A broad `ACCEPT` rule placed before a more specific `DROP` rule will make the `DROP` rule irrelevant for anything the earlier rule already matched.

Persisting `iptables`/`nftables` rules across reboots requires an explicit save step (e.g., `iptables-save`, or a distribution-specific persistence package/service) — rules configured directly on the command line are otherwise lost on reboot, a very common source of "it worked, then stopped working after a restart" confusion.

---

## Tips
- Always have a second, independent access path (console, cloud provider serial console, physical access) before making firewall changes on a remote system — treat every firewall change as potentially lockout-risky until proven otherwise.
- Use `ufw`/`firewalld` for the common case; drop to raw `iptables`/`nftables` only when you need fine-grained control they don't expose.
- Test rules incrementally — add one rule, verify its effect with tools from Lesson 03 (`ss`, `nmap`, `curl`), then move to the next, rather than writing a large rule set all at once and debugging it as a whole.

## Common Mistakes
- Enabling a default-deny firewall on a remote system without first allowing SSH, causing an immediate lockout.
- Forgetting to persist `iptables`/`nftables` rules, losing all custom configuration on the next reboot.
- Placing a broad allow rule before a more specific deny rule, silently making the deny rule ineffective.
- Using `0.0.0.0/0` (or no source restriction at all) for a rule that should really be limited to a specific trusted IP range.
- Forgetting `--permanent` (and the subsequent `--reload`) with `firewall-cmd`, losing changes on the next reload.

## Common Misconceptions
- **"A firewall alone makes a service secure."** A firewall controls *reachability*, not the security of the service itself — a reachable, vulnerable service is still vulnerable; the firewall just narrows who can attempt to exploit it.
- **"Default-allow is fine as long as I remember to block the bad stuff."** Default-allow requires you to anticipate every future risk in advance and never forget to add a block rule for it; default-deny only requires remembering to allow what you actually need, which is a fundamentally safer failure mode.
- **"Outbound traffic doesn't need firewall rules — only inbound matters."** Restricting outbound traffic (egress filtering) is an important and often-overlooked control, since it can limit an attacker's ability to exfiltrate data or establish command-and-control communication even after an initial compromise.

## Quick Practice
```bash
sudo ufw status verbose
# or
sudo firewall-cmd --list-all
# or
sudo iptables -L -n -v
```
Identify your lab system's current firewall tool, its default policy, and every currently active allow rule.

## Knowledge Check
1. Why is a default-deny policy generally considered more secure than default-allow?
2. What's the critical precaution to take before enabling a default-deny firewall on a system you're connected to remotely?
3. In `iptables`, why does rule order matter within a chain?
4. What does the `--permanent` flag do in `firewall-cmd`, and what happens if you forget it?
5. Why might restricting outbound (egress) traffic be valuable even if inbound rules are already well configured?

<details>
<summary>Answers</summary>

1. Default-deny fails safe — anything not explicitly allowed is blocked automatically, including new or unexpected services — while default-allow fails open, exposing anything not explicitly blocked.
2. Explicitly allow SSH (or your current access method) *before* enabling the default-deny policy, to avoid immediately locking yourself out.
3. Chains are evaluated top to bottom and the first matching rule determines the outcome for iptables; a broad rule placed earlier can make a more specific rule placed later irrelevant for overlapping traffic.
4. `--permanent` saves the rule so it survives a reload/reboot; without it, the rule takes effect immediately but is lost the next time the firewall configuration is reloaded or the system restarts.
5. Egress filtering can limit an attacker's ability to exfiltrate data or maintain command-and-control communication even after they've gained a foothold, adding a layer of defense beyond just preventing initial inbound access.
</details>

## Best Practices
- Default to a default-deny inbound policy, allowing only specifically required services and ports.
- Always confirm an alternate access path exists before applying firewall changes remotely.
- Scope allow rules as narrowly as possible (specific ports, and specific source IP ranges where feasible) rather than broad catch-all rules.
- Persist firewall rules explicitly and verify they survive a reboot in a test environment before relying on them in production.
- Apply egress filtering where practical, not just ingress filtering.
- Regularly audit active rules against documented, expected configuration to catch drift or leftover temporary rules.

## Summary
Host-based firewalls, built on Linux's netfilter framework and accessible through `ufw`, `firewalld`, or raw `iptables`/`nftables`, are the mechanism that actually enforces which network traffic reaches a host and which is allowed out. A default-deny policy with narrowly scoped allow rules is the consistently recommended baseline, and understanding rule ordering, persistence, and the critical importance of not locking yourself out remotely ties together everything else covered in this module — addressing, remote access, diagnostics, and name resolution all become concretely enforceable through firewall configuration.

## What's Next?
This wraps up the core lesson content for the module. From here, use the module-wide **[Troubleshooting Guide](troubleshooting-guide.md)** whenever you hit an unexpected networking or firewall issue, and keep the **[Cheat Sheet](cheatsheet.md)** open as a quick reference while you continue practicing.


---

## Navigation
⬅️ [Previous: Lesson 04 – DNS](04-dns.md) | [ Lab 05: Firwalls-basics](../../labs/module-03/05-firewall-basics.md) | ➡️ [Troubleshooting Guide](troubleshooting-guide.md)
