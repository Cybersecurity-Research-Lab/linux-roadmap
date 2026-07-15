# 🧪 Lab 04 – Exploring the Linux File System

> Learn the purpose of the most important Linux directories and understand how Linux organizes files using the File System Hierarchy Standard (FHS).

---

#  Lab Objectives

By the end of this lab, you will be able to:

* Identify the root directory.
* Locate important Linux system directories.
* Explain the purpose of each major directory.
* Understand where Linux stores users, applications, logs, and configuration files.
* Connect the Linux file system hierarchy to real-world cybersecurity tasks.

---

## ⏱ Estimated Time

**30–45 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Related Lesson

**Lesson 04 – Linux File System**

---

# 🛠 Requirements

* Ubuntu Virtual Machine
* Terminal
* Internet **not required**

---

#  Before You Start

Open your Linux terminal.

You do **not** need to modify any system files during this lab.

This lab is about exploration and observation.

---

#  Exercise 1 – View the Root Directory

Run:

```bash
ls /
```

### ✅ Expected Result

You should see directories similar to:

```text
bin
boot
dev
etc
home
media
mnt
opt
proc
root
run
srv
sys
tmp
usr
var
```

Your distribution may contain a few additional directories.

---

#  Exercise 2 – Find Your Home Directory

Run:

```bash
echo $HOME
```

### ✅ Expected Result

Example:

```text
/home/username
```

This is your personal workspace.

---

# Exercise 3 – Explore Important Directories

For each command below, observe the output.

```bash
ls /etc
```

```bash
ls /home
```

```bash
ls /var
```

```bash
ls /usr
```

```bash
ls /tmp
```

You don't need to understand every file yet.

Simply notice that each directory serves a different purpose.

---

# 🚀 Exercise 4 – Locate the Current User

Run:

```bash
whoami
```

Now compare the result with:

```bash
ls /home
```

### ✅ Expected Result

Your username should appear as one of the folders inside `/home`.

---

# 🚀 Exercise 5 – Temporary Files

Run:

```bash
ls /tmp
```

You may see:

* nothing
* a few temporary files
* several temporary directories

This is normal.

Linux applications use `/tmp` while they are running.

---

# 🚀 Exercise 6 – Configuration Files

Run:

```bash
ls /etc
```

Notice the large number of configuration files.

Examples may include:

```text
passwd
hostname
hosts
ssh
```

Do **not** edit anything.

---

# 🚀 Exercise 7 – Log Directory

Run:

```bash
ls /var/log
```

### ✅ Expected Result

You should see log files similar to:

```text
syslog
auth.log
kern.log
```

(Some distributions use different names.)

These logs are extremely important during cybersecurity investigations.

---

#  Exercise 8 – Applications

Run:

```bash
ls /usr
```

Notice that this directory contains many application-related folders.

Linux stores most installed software here.

---

# 🧠 Self-Check

Without searching online, answer the following:

1. Which directory is the top of the Linux file system?
2. Where are user files stored?
3. Which directory stores configuration files?
4. Which directory stores log files?
5. Which directory stores temporary files?
6. Which directory usually contains installed applications?

---

# 💡 Mini Challenge

Fill in the table below.

| Directory | Purpose    |
| --------- | ---------- |
| `/`       | __________ |
| `/home`   | __________ |
| `/etc`    | __________ |
| `/var`    | __________ |
| `/usr`    | __________ |
| `/tmp`    | __________ |
| `/boot`   | __________ |
| `/dev`    | __________ |
| `/proc`   | __________ |

Try to complete the table **without opening the lesson**.

---

#  Cybersecurity Challenge

Imagine you're investigating a compromised Linux server.

Match each task with the directory you would most likely inspect first.

| Investigation              | Directory |
| -------------------------- | --------- |
| User documents             | ______    |
| Authentication logs        | ______    |
| System configuration       | ______    |
| Temporary suspicious files | ______    |
| Installed software         | ______    |

---

# 📸 Screenshot Challenge

Take screenshots of:

* The output of `ls /`
* The output of `ls /etc`
* The output of `ls /var/log`
* The output of `ls /usr`

Save them inside:

```text
assets/screenshots/module-01/
```

These screenshots can later be added to this lab to help future learners.

---

# 🏁 Lab Complete

Congratulations!

You now understand how Linux organizes its files and directories and where important system information is stored.

This knowledge will be essential throughout the rest of your Linux and cybersecurity journey.

Continue with [Lesson 05 – Files and Directories](../../docs/Module-01-Foundations/05-Files-and-Directories.md)
