# 🐚 Bash Scripting for Cybersecurity Professionals
### A Hands-On Automation Module

---

## 📋 Module Overview

This module builds core Bash scripting skills every cybersecurity professional needs to automate repetitive tasks, build custom tooling, and understand how attackers and defenders alike use scripting on Linux systems. Every lesson follows the same predictable structure so you always know what's coming, and every lesson ends with a hands-on lab so the concepts move from "I read it" to "I can do it."

By the end of this module, you will be able to write robust Bash scripts using variables, control flow, and reusable functions, build practical automation for administrative and security tasks, and schedule that automation reliably using cron — while understanding how each of these mechanisms shows up on both sides of real-world security incidents.

---

## 🎯 Module Learning Objectives

- Write, execute, and debug Bash scripts confidently
- Use variables, parameters, and quoting correctly and safely
- Control script flow with loops and conditionals
- Organize code into reusable, well-structured functions
- Build practical automation scripts for real administrative and security tasks
- Schedule recurring tasks reliably using cron, and recognize cron-based persistence techniques

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
 ├─ Main Topic 3 
 ├─ Tips
 ├─ Common Mistakes
 ├─ Common Misconceptions
 ├─ Quick Practice
 ├─ Knowledge Check
 ├─ Best Practices
 ├─ Summary
 ├─ What's Next?
 └─ Navigation
```

---

## 📚 Module Contents

| # | Lesson | Difficulty | Est. Time |
|---|--------|-----------|-----------|
| 01 | [Bash Fundamentals](01-bash-fundamentals.md) | 🟢 Beginner | 45 min |
| 02 | [Variables](02-variables.md) | 🟢 Beginner | 45 min |
| 03 | [Loops](03-loops.md) | 🟡 Beginner–Intermediate | 50 min |
| 04 | [Functions](04-functions.md) | 🟡 Intermediate | 50 min |
| 05 | [Bash Automation](05-bash-automation.md) | 🟡 Intermediate | 55 min |
| 06 | [Cron Jobs](06-cron-jobs.md) | 🟡 Intermediate | 45 min |

**Supporting References (module-wide):**
- 🛠️ [Troubleshooting Guide](../../Troubleshooting/Module-04.md) — common failures across every lesson topic and how to diagnose them
- 📑 [Cheat Sheet](../../Cheatsheet/Module-04.md) — one-page quick reference for every command and syntax pattern covered in the module

**Total estimated module time:** ~5 hours (including labs)

---

## 🧭 Recommended Path

Lessons are ordered so each one builds on the last:

```
01 Bash Fundamentals → 02 Variables → 03 Loops → 04 Functions
        → 05 Bash Automation → 06 Cron Jobs
```

You can jump directly to any lesson if you already have the prerequisite knowledge listed at the top of it, but working through in order is recommended for anyone new to shell scripting.

---

## 🧪 Lab Environment Requirements

All labs assume access to a Linux system (physical, virtual machine, container, or cloud instance) with a standard Bash shell (`bash --version` to confirm) and enough permissions to create and execute scripts in your home directory. A disposable VM or container is recommended for the Bash Automation and Cron Jobs labs, since they involve scheduled, unattended execution that's easier to review in a low-stakes environment.

Suggested setups:
- A local VM (VirtualBox/VMware) running Ubuntu Server or Debian
- A cloud free-tier instance (AWS EC2, GCP, Azure)
- A Docker container (note: some distros ship `sh`/`dash` as default; explicitly invoke `bash` if unsure)

---

## ✅ How to Use This Module

1. Read the lesson start to finish before touching a terminal — the "why" matters as much as the "how."
2. Do the **Quick Practice** as you go; it's short and reinforces the immediately preceding topic.
3. Complete the **Hands-On Lab** at the end of every lesson before moving on.
4. Use the **Knowledge Check** to confirm you're ready to advance.
5. Keep the **Cheat Sheet** open in a second window while you work — it's built for that.
6. When something breaks (it will — Bash's quoting and word-splitting rules trip up everyone at first), start with the **Troubleshooting Guide** before searching the web.

---

## 🚀 Get Started

Begin with **[Lesson 01 – Bash Fundamentals](01-bash-fundamentals.md)**
