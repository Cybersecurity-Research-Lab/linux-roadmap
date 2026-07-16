# 🌐 Linux Networking Basics for Cybersecurity Professionals
### A Hands-On Systems Administration Module

---

## 📋 Module Overview

This module builds the core Linux networking skills every cybersecurity professional needs before tackling network security monitoring, penetration testing, or incident response. Every lesson follows the same predictable structure so you always know what's coming, and every lesson ends with a hands-on lab so the concepts move from "I read it" to "I can do it."

By the end of this module, you will be able to confidently inspect and configure network interfaces, connect to and harden remote systems over SSH, use core network diagnostic and reconnaissance utilities, understand how DNS resolution works (and how it's abused), and configure host-based firewalls to control traffic in and out of a Linux system.

---

## 🎯 Module Learning Objectives

- Explain core networking concepts (IP addressing, interfaces, routing, ports) as they apply to Linux
- Configure, use, and harden SSH for secure remote access
- Use network utilities (`ping`, `traceroute`, `netstat`/`ss`, `curl`, `nmap`, `tcpdump`) for diagnostics and reconnaissance
- Explain how DNS resolution works and recognize DNS-based attack techniques
- Configure host-based firewalls (`iptables`, `nftables`, `ufw`/`firewalld`) to control network traffic

---

## 🗂️ Lesson Structure

Every lesson in this module follows the same algorithm, so you can navigate efficiently and always know where to find what you need:

```
📖 Lesson XX – Lesson Title
 ├─ Learning Outcomes
 ├─ Estimated Study Time
 ├─ Difficulty
 ├─ Prerequisites
 ├─ Key Terms
 ├─ Introduction
 ├─ Why This Matters
 ├─ Cybersecurity Insight
 ├─ Researcher's Note
 ├─ Real-World Example
 ├─ Main Topic 1
 ├─ Main Topic 2
 ├─ Main Topic 3 (repeated as needed)
 ├─ Tips
 ├─ Common Mistakes
 ├─ Common Misconceptions
 ├─ Quick Practice
 ├─ Knowledge Check
 ├─ Best Practices
 ├─ Summary
 ├─ What's Next?
 ├─ 🧪 Hands-On Lab
 └─ Navigation
```

---

## 📚 Module Contents

| # | Lesson | Difficulty | Est. Time |
|---|--------|-----------|-----------|
| 01 | [Linux Networking Basics](01-linux-networking-basics.md) | 🟢 Beginner | 50 min |
| 02 | [SSH](02-ssh.md) | 🟡 Intermediate | 55 min |
| 03 | [Network Utilities](03-network-utilities.md) | 🟡 Intermediate | 60 min |
| 04 | [DNS](04-dns.md) | 🟡 Intermediate | 50 min |
| 05 | [Firewall Basics](05-firewall-basics.md) | 🔴 Intermediate–Advanced | 60 min |

**Supporting References (module-wide):**
- 🛠️ [Troubleshooting Guide](troubleshooting-guide.md) — common failures across every lesson topic and how to diagnose them
- 📑 [Cheat Sheet](cheatsheet.md) — one-page quick reference for every command covered in the module

**Total estimated module time:** ~4.5–5 hours (including labs)

---

## 🧭 Recommended Path

Lessons are ordered so each one builds on the last:

```
01 Networking Basics → 02 SSH → 03 Network Utilities → 04 DNS → 05 Firewall Basics
```

You can jump directly to any lesson if you already have the prerequisite knowledge listed at the top of it, but working through in order is recommended for anyone new to Linux networking.

---

## 🧪 Lab Environment Requirements

All labs assume access to a Linux system (physical, virtual machine, container, or cloud instance) where you have `sudo` privileges. A disposable VM is strongly recommended — several labs involve modifying SSH configuration and firewall rules, and misconfiguring either can lock you out of a system you depend on.

Suggested setups:
- Two networked lab VMs (one acting as "client," one as "server") give the most realistic experience, especially for the SSH and Network Utilities lessons
- A local VM (VirtualBox/VMware) running Ubuntu Server or Debian
- A cloud free-tier instance (AWS EC2, GCP, Azure) — useful for realistic firewall/security-group practice, but be careful: always keep a working console/serial access path in case a firewall rule locks out SSH

⚠️ **Before editing firewall or SSH configuration on any remote system, always confirm you have an out-of-band access method (console access, cloud provider serial console, or physical access) in case you lock yourself out.**

---

## ✅ How to Use This Module

1. Read the lesson start to finish before touching a terminal — the "why" matters as much as the "how."
2. Do the **Quick Practice** as you go; it's short and reinforces the immediately preceding topic.
3. Complete the **Hands-On Lab** at the end of every lesson before moving on.
4. Use the **Knowledge Check** to confirm you're ready to advance.
5. Keep the **Cheat Sheet** open in a second window while you work — it's built for that.
6. When something breaks (it will — especially with firewalls and SSH), start with the **Troubleshooting Guide** before searching the web.

---

## 🚀 Get Started

Begin with **[Lesson 01 – Linux Networking Basics](01-linux-networking-basics.md)**.
