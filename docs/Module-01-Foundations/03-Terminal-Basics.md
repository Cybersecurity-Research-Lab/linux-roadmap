# 📖 Lesson 03 – Terminal Basics

> Learn how to interact with Linux through the command line and understand why the terminal is one of the most powerful tools in cybersecurity.

---

# Learning Outcomes

After completing this lesson, you will be able to:

* Understand what the Linux terminal is.
* Explain the difference between the Terminal and the Shell.
* Open and navigate the terminal.
* Understand the command prompt.
* Execute basic Linux commands.
* Understand command syntax.
* Use built-in documentation.
* Explain why cybersecurity professionals rely on the command line.

---

## ⏱️ Estimated Study Time

**45–60 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Prerequisites

* Lesson 01 – Introduction to Linux
* Lesson 02 – Installing Linux

---

# 📚 What Is the Terminal?

The **Terminal** is an application that allows you to communicate with your operating system by typing commands instead of using a graphical interface.

Unlike clicking buttons in a desktop environment, the terminal provides direct access to Linux and allows you to perform tasks quickly, efficiently, and with greater control.

Many operations that would require several clicks in a graphical interface can often be completed with a single command.

---

# 🖥️ Terminal vs Shell

These two terms are often confused, but they are not the same.

## Terminal

The **Terminal** is the application (window) where you type commands.

Think of it as the interface between you and the operating system.

Examples include:

* GNOME Terminal
* Konsole
* xterm

---

## Shell

The **Shell** is the program that reads, interprets, and executes the commands you type.

When you enter:

```bash
pwd
```

the shell processes the command and asks the Linux kernel to execute it.

Ubuntu uses **Bash (Bourne Again SHell)** as its default shell.

Later in this roadmap you'll learn Bash scripting in depth.

---

# Why Learn the Terminal?

Using the terminal allows you to:

* Work faster.
* Automate repetitive tasks.
* Manage remote systems.
* Troubleshoot problems.
* Use professional cybersecurity tools.
* Control Linux more efficiently than a graphical interface.

The terminal is one of the most valuable skills for Linux users because almost every advanced Linux feature can be accessed from it.

---

# 🌍 Why This Matters

The Linux terminal is one of the most fundamental skills in Linux, cloud computing, system administration, DevOps, and cybersecurity.

While graphical interfaces are convenient for everyday tasks, professionals rely heavily on the terminal because it provides greater speed, flexibility, and complete control over the operating system.

Every module that follows in this roadmap—including file management, permissions, networking, Bash scripting, SSH, automation, Linux security, and cloud administration—builds upon the concepts introduced in this lesson.

Mastering the terminal now will make learning everything else significantly easier.

---

# 🛡️ Cybersecurity Insight

The command line is one of the primary tools used by cybersecurity professionals every day.

Security analysts, penetration testers, incident responders, digital forensic investigators, and system administrators spend a significant amount of their work inside the terminal because it allows them to automate tasks, analyze systems, investigate incidents, and manage remote infrastructure efficiently.

Many industry-standard cybersecurity tools are designed primarily for command-line use, including:

* **Nmap** – Network discovery and security auditing
* **Metasploit Framework** – Penetration testing
* **John the Ripper** – Password auditing
* **Hashcat** – Password recovery
* **OpenSSH** – Secure remote administration
* **tcpdump** – Network traffic analysis
* **Wazuh CLI utilities** – Security monitoring and incident response

Learning the terminal is not about memorizing commands—it's about understanding how to communicate effectively with your operating system.

---

> 💡 **Researcher's Note**
>
> Don't try to memorize every command. Focus on understanding what each command does, when it should be used, and how to find its documentation. Even experienced Linux professionals regularly consult manuals and official documentation.

---

# Opening the Terminal

There are multiple ways to open the terminal.

## Method 1

Use the keyboard shortcut:

```text
Ctrl + Alt + T
```

---

## Method 2

1. Open **Activities**.
2. Search for **Terminal**.
3. Press **Enter**.

---

# Understanding the Prompt

When the terminal opens, you'll usually see something similar to:

```bash
name@ubuntu:~$
```

Each part has a specific meaning.

| Part   | Meaning                  |
| ------ | ------------------------ |
| name   | Current user             |
| ubuntu | Computer hostname        |
| ~      | Current directory (Home) |
| $      | Normal user              |

If you are logged in as the **root** user, the prompt ends with:

```text
#
```

instead of

```text
$
```

---

# Your First Commands

## Print Working Directory

```bash
pwd
```

Displays the current directory.

---

## Display Current User

```bash
whoami
```

Displays the currently logged-in user.

---

## Display Kernel Version

```bash
uname -r
```

Shows the Linux kernel version.

---

## List Files

```bash
ls
```

Displays the contents of the current directory.

---

# Command Syntax

Most Linux commands follow the same structure:

```text
command [options] [arguments]
```

Example:

```bash
ls -l Documents
```

| Component | Meaning  |
| --------- | -------- |
| ls        | Command  |
| -l        | Option   |
| Documents | Argument |

Understanding this structure will make learning future Linux commands much easier.

---

# Getting Help

Linux provides built-in documentation for almost every command.

View the manual page:

```bash
man ls
```

Or display a quick help message:

```bash
ls --help
```

One of the most valuable Linux skills is learning how to use documentation effectively.

---

# Best Practices

✔ Read command output carefully.

✔ Don't copy commands you don't understand.

✔ Practice every command yourself.

✔ Read the official documentation whenever possible.

✔ Make mistakes and learn from them.

---

# Summary

In this lesson you learned:

* What the terminal is.
* The difference between the terminal and the shell.
* Why Linux professionals rely on the command line.
* How to open the terminal.
* How to understand the command prompt.
* Your first Linux commands.
* Command syntax.
* How to use built-in documentation.

---

## 🧭 Navigation

⬅️ **Previous Lesson**

📖![Lesson 02 – Installing Linux](02-Installing-Linux.md)

➡️ **Next Lesson**

📖 ![Lesson 04 – Linux File System](04-Linux-file-system.md)

💻 **Lab**

![Lab 03 – Terminal Basics](../../labs/lab-03-Terminal_basics)

📄 **Cheat Sheet**

![Terminal Basics Cheat Sheet](../../cheatsheet/Terminal-basics)

🛠️ **Troubleshooting**

![Module 01 Troubleshooting](../../Troubleshooting/module01)

📚 **Resources**

![Terminal Basics Resources](README.md)
