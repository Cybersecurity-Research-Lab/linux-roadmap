# 🐧 Linux Fundamentals for Cybersecurity Professionals
### A Hands-On Systems Administration Module

---

## 📋 Module Overview

This module builds the core Linux systems administration skills that every cybersecurity professional needs before tackling security-specific tools and techniques. Every lesson follows the same predictable structure so you always know what's coming, and every lesson ends with a hands-on lab so the concepts move from "I read it" to "I can do it."

By the end of this module, you will be able to confidently manage users, control access through permissions, work with running processes and services, install and audit software packages, manipulate environment variables, and chain commands together using redirection and pipes — all core skills for system hardening, incident response, and penetration testing.

---

## 🎯 Module Learning Objectives

- Administer Linux user and group accounts, including privilege boundaries
- Read, interpret, and modify file and directory permissions (including special bits)
- Enumerate, monitor, and control processes
- Manage system services and understand persistence mechanisms attackers abuse
- Use package managers to install, update, remove, and audit software
- Understand and manipulate environment variables safely
- Use shell redirection and pipes to build efficient command-line workflows

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
| 01 | [Users and Groups](01-users-and-groups.md) | 🟢 Beginner | 45 min |
| 02 | [File Permissions](02-file-permissions.md) | 🟡 Beginner–Intermediate | 60 min |
| 03 | [Processes](03-processes.md) | 🟡 Intermediate | 50 min |
| 04 | [Services](04-services.md) | 🟡 Intermediate | 50 min |
| 05 | [Package Managers](05-package-managers.md) | 🟢 Beginner–Intermediate | 45 min |
| 06 | [Environment Variables](06-environment-variables.md) | 🟡 Intermediate | 40 min |
| 07 | [Shell Redirection & Pipes](07-shell-redirection-pipes.md) | 🔴 Intermediate–Advanced | 55 min |

**Supporting References (module-wide):**
- 🛠️ [Troubleshooting Guide](troubleshooting-guide.md) — common failures across every lesson topic and how to diagnose them
- 📑 [Cheat Sheet](cheatsheet.md) — one-page quick reference for every command covered in the module

**Total estimated module time:** ~5.5–6 hours (including labs)

---

## 🧭 Recommended Path

Lessons are ordered so each one builds on the last:

```
01 Users & Groups → 02 File Permissions → 03 Processes → 04 Services
        → 05 Package Managers → 06 Environment Variables → 07 Redirection & Pipes
```

You can jump directly to any lesson if you already have the prerequisite knowledge listed at the top of it, but working through in order is recommended for anyone new to Linux.

---

## 🧪 Lab Environment Requirements

All labs assume access to a Linux system (physical, virtual machine, container, or cloud instance) where you have `sudo` privileges. A disposable VM or container is strongly recommended — several labs involve creating users, changing permissions, and stopping/starting services, and you don't want to do that on a system you depend on.

Suggested setups:
- A local VM (VirtualBox/VMware) running Ubuntu Server or Debian
- A cloud free-tier instance (AWS EC2, GCP, Azure)
- A Docker container for lower-risk experimentation (note: some process/service labs behave differently in containers — this is called out where relevant)

---

## ✅ How to Use This Module

1. Read the lesson start to finish before touching a terminal — the "why" matters as much as the "how."
2. Do the **Quick Practice** as you go; it's short and reinforces the immediately preceding topic.
3. Complete the **Hands-On Lab** at the end of every lesson before moving on.
4. Use the **Knowledge Check** to confirm you're ready to advance.
5. Keep the **Cheat Sheet** open in a second window while you work — it's built for that.
6. When something breaks (it will), start with the **Troubleshooting Guide** before searching the web.

---

## 🚀 Get Started

Begin with **[Lesson 01 – Users and Groups](01-users-and-groups.md)**.
