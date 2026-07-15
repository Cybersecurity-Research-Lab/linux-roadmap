#  Lesson 04 – Linux File System

> Learn how Linux organizes files and directories, understand the Linux File System Hierarchy (FHS), and confidently navigate your operating system using the command line.

---

#  Learning Outcomes

After completing this lesson, you will be able to:

* Explain how the Linux file system is organized.
* Understand the Linux File System Hierarchy (FHS).
* Distinguish between files and directories.
* Navigate the Linux file system using the terminal.
* Understand absolute and relative paths.
* Identify the purpose of the most important Linux directories.
* Recognize hidden files and directories.
* Explain why understanding the file system is essential for cybersecurity.

---

## ⏱️ Estimated Study Time

**60–75 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Prerequisites

* [Lesson 01 – Introduction to Linux](01-Introduction-to-Linux.md)
* [Lesson 02 – Installing Linux](02-Installing-Linux.md)
* [Lesson 03 – Terminal Basics](03-Terminal-Basics.md)

---

#  Key Terms

| Term                        | Definition                                              |
| --------------------------- | ------------------------------------------------------- |
| File                        | A container that stores data.                           |
| Directory                   | A folder used to organize files.                        |
| Root Directory              | The top-level directory of Linux (`/`).                 |
| Home Directory              | Personal workspace for each user.                       |
| Absolute Path               | A complete path beginning with `/`.                     |
| Relative Path               | A path relative to the current location.                |
| Hidden File                 | A file beginning with a period (`.`).                   |
| File System Hierarchy (FHS) | The standard structure used by Linux to organize files. |

---

# 📚 Introduction

Every operating system needs a way to organize information.

Linux stores everything—including programs, documents, devices, and even running processes—as files organized into directories.

Unlike Windows, Linux follows a standardized directory structure known as the **File System Hierarchy Standard (FHS)**. Once you understand this hierarchy, you'll know where configuration files, applications, user data, logs, and system files are stored on almost any Linux distribution.

Understanding the Linux file system is one of the most important skills you'll learn because nearly every administrative and cybersecurity task depends on navigating it correctly.

---

#  Why This Matters

The Linux file system is the foundation of the operating system.

Every command you execute, every configuration file you modify, every application you install, and every security investigation you perform involves interacting with files and directories.

Learning the Linux file system early will make future topics—such as permissions, Bash scripting, networking, SSH, logging, system administration, and incident response—much easier to understand.

---

# 🛡️ Cybersecurity Insight

Security professionals constantly investigate files.

During an incident response investigation, an analyst might need to:

* Examine system logs.
* Search configuration files.
* Locate malware.
* Inspect user directories.
* Analyze web server files.
* Recover deleted information.
* Review authentication records.

Without understanding the Linux file system, these tasks become extremely difficult.

Knowing where important files are stored is one of the first practical skills every Linux security professional develops.

---

> 💡 **Researcher's Note**
>
> Don't try to memorize every directory immediately. Focus on understanding the purpose of each location. As you continue practicing Linux, these directories will become familiar naturally.

---

# 🗂️ Files vs Directories

A **file** stores information.

Examples:

* Documents
* Images
* Programs
* Configuration files
* Databases

A **directory** stores other files and directories.

Think of a directory as a container used to organize information.

Example:

```text
Documents/
├── Notes.txt
├── Linux.pdf
└── Projects/
```

---

#  The Linux File System Hierarchy

Linux organizes everything under a single top-level directory called the **Root Directory**.

```
/
├── bin
├── boot
├── dev
├── etc
├── home
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── srv
├── sys
├── tmp
├── usr
└── var
```

Unlike Windows, Linux does **not** use drive letters such as:

```
C:
D:
E:
```

Everything begins from:

```
/
```

---

#  Root Directory

The **root directory** (`/`) is the highest directory in Linux.

Every file and directory exists somewhere underneath it.

Think of it as the trunk of a tree, with every other directory branching from it.

---

#  Home Directory

Every user has a personal home directory.

Example:

```
/home/name
```

This is where you store:

* Documents
* Downloads
* Desktop files
* Projects
* Personal configuration files

The shortcut:

```
~
```

always refers to your current user's home directory.

Example:

```
cd ~
```

takes you directly home.

---

# 📂 Important Linux Directories

## `/bin`

Contains essential user commands.

Examples:

* ls
* cp
* mv
* cat

---

## `/boot`

Contains files required for booting Linux.

Examples:

* Linux kernel
* Bootloader files

---

## `/dev`

Contains device files.

Examples:

* Hard drives
* USB devices
* Keyboards
* Terminals

In Linux, devices are treated as files.

---

## `/etc`

Stores system-wide configuration files.

Examples:

* Network configuration
* User accounts
* Services
* DNS settings

This is one of the most important directories for Linux administrators.

---

## `/home`

Stores user data.

Each user has their own directory.

Example:

```
/home/alice
/home/bob
```

---

## `/opt`

Contains optional third-party software.

Applications installed manually are often placed here.

---

## `/usr`

Contains most user applications, libraries, documentation, and shared resources.

Although the name suggests "user," it actually contains many of the programs installed on the system.

---

## `/var`

Stores data that changes frequently.

Examples:

* Log files
* Mail
* Databases
* Cache

Security analysts spend a lot of time inside this directory.

---

## `/tmp`

Stores temporary files.

Many programs use it while running.

Its contents may be deleted automatically after a reboot.

---

## `/proc`

A virtual file system containing information about the running system.

It provides details about:

* Processes
* CPU
* Memory
* Kernel information

Unlike normal directories, most files here are generated dynamically by the kernel.

---

## `/dev`

Contains representations of hardware devices.

Everything from disks to terminals appears here as a file.
---

#  Understanding the Linux File System

Unlike Windows, where files are often organized around different drives (such as `C:` or `D:`), Linux uses a **single unified directory tree**.

Every file, directory, storage device, and mounted partition becomes part of this tree under the root directory (`/`).

This design makes Linux more consistent and flexible because no matter where data is stored, it is always accessed through the same directory hierarchy.

For example:

```text
/
├── home
├── etc
├── var
└── usr
```

Everything begins from the root directory.

---

#  Why Is the File System Organized This Way?

The Linux File System Hierarchy Standard (FHS) provides consistency across Linux distributions.

This means that whether you use Ubuntu, Debian, Fedora, Rocky Linux, or another distribution, important files are generally stored in the same locations.

Benefits include:

* Easier system administration.
* Better software compatibility.
* Faster troubleshooting.
* Consistent server management.
* Simpler automation.

Understanding this structure allows you to work confidently on almost any Linux system.

---

# 🔐 Security Perspective

Knowing where important files are stored is essential for both Linux administration and cybersecurity.

For example:

| Directory  | Security Importance                      |
| ---------- | ---------------------------------------- |
| `/etc`     | System configuration files               |
| `/var/log` | Security logs and authentication records |
| `/home`    | User data and personal files             |
| `/tmp`     | Temporary files that malware may abuse   |
| `/boot`    | Bootloader and kernel files              |
| `/proc`    | Information about running processes      |

Many security investigations begin by examining files located in these directories.

---

# ❌ Common Misconceptions

### "The root directory and the root user are the same thing."

No.

* **Root directory (`/`)** is the top-level directory of the Linux file system.
* **Root user** is the administrator account with full system privileges.

Although they share the word *root*, they refer to completely different concepts.

---

### "Everything inside Linux is stored in the Home directory."

No.

The Home directory only stores personal user files.

System files, applications, logs, configuration files, and devices are stored in many different directories throughout the file system.

---

### "Deleting a file from `/tmp` will always cause problems."

Not necessarily.

The `/tmp` directory is specifically intended for temporary files that applications create while running.

Many of these files are automatically removed after a reboot.

---

#  Quick Practice

Without using the internet, answer the following questions:

1. What is the highest directory in Linux?
2. Which directory stores user files?
3. Which directory contains system configuration files?
4. Which directory stores temporary files?
5. Which directory often contains system log files?
6. Why does Linux use a standardized file system hierarchy?

If you can answer these questions confidently, you've understood the core concepts of the Linux file system.

---

# ✅ Best Practices

✔ Learn the purpose of directories instead of memorizing every file.

✔ Avoid modifying system directories unless you understand their purpose.

✔ Store personal work inside your Home directory.

✔ Use official documentation when exploring unfamiliar directories.

✔ Remember that every Linux distribution follows the File System Hierarchy Standard with only minor differences.

---

# 📝 Summary

In this lesson you learned:

* What the Linux file system is.
* The difference between files and directories.
* The Linux File System Hierarchy (FHS).
* The purpose of the root directory.
* The purpose of the Home directory.
* The function of the most important Linux directories.
* Why understanding the Linux file system is important for system administration and cybersecurity.

---

#  What's Next?

Now that you understand **how Linux organizes information**, it's time to learn **how to interact with it**.

In the next lesson, you'll learn how to:

* Navigate directories.
* Understand absolute and relative paths.
* Use commands such as `pwd`, `cd`, and `ls`.
* Explore hidden files.
* Move efficiently through the Linux file system.

---

## 🧭 Navigation

⬅️ **Previous Lesson**

[ Lesson 03 – Terminal Basics](03-Terminal-Basics.md)

➡️ **Next Lesson**

[Lesson 05 – Files and Directories](05-Files-and-Directories.md)

💻 **Lab**

[Lab 04 – Exploring the Linux File System](../../labs/module-01/lab-04-Linux-File-system.md)

📄 **Cheat Sheet**

[Linux File System Cheat Sheet](../../cheatsheet/linux-file-systen.md)

🛠️ **Troubleshooting**

[Module 01 Troubleshooting](../../Troubleshooting/module-01.md)

📚 **Resources**

[Linux File System Resources](README.md)
