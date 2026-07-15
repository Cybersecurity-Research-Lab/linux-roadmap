#  Lesson 05 – Files and Directories

> Learn how to navigate the Linux file system, understand paths, and work confidently with files and directories using the command line.

---

#  Learning Outcomes

After completing this lesson, you will be able to:

* Navigate the Linux file system using the terminal.
* Understand the current working directory.
* Differentiate between absolute and relative paths.
* Move between directories using `cd`.
* Display your current location using `pwd`.
* List directory contents using `ls`.
* Understand hidden files and directories.
* Use Linux shortcuts such as `.`, `..`, and `~`.
* Use tab completion to work more efficiently.

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
* [Lesson 04 – Linux File System](04-Linux-File-Directories.md)

---

#  Key Terms

| Term                      | Definition                                                      |
| ------------------------- | --------------------------------------------------------------- |
| Current Working Directory | The directory you are currently inside.                         |
| Absolute Path             | A complete path beginning from the root directory (`/`).        |
| Relative Path             | A path relative to your current directory.                      |
| Home Directory            | Your personal workspace (`~`).                                  |
| Hidden File               | A file whose name begins with a period (`.`).                   |
| Parent Directory          | The directory one level above your current location (`..`).     |
| Tab Completion            | A feature that automatically completes commands and file names. |

---

#  Introduction

Now that you understand how Linux organizes files and directories, it's time to learn how to move through them.

Navigating the file system is one of the first practical Linux skills. Whether you're editing configuration files, analyzing logs, writing scripts, or investigating a compromised system, you'll constantly move between directories.

Mastering navigation will make every future lesson significantly easier.

---

#  Why This Matters

Every Linux task begins with knowing **where you are** and **where you want to go**.

Administrators use navigation commands to manage servers.

Developers use them to access projects.

Cybersecurity professionals use them to investigate systems, analyze logs, locate evidence, and inspect configurations.

Learning to navigate efficiently saves time and reduces mistakes.

---

#  Cybersecurity Insight

During a security investigation, analysts rarely use graphical interfaces.

Instead, they move through the system using the terminal to:

* Inspect log files.
* Locate suspicious programs.
* Review configuration files.
* Search user directories.
* Examine web server files.
* Investigate malware locations.

Fast navigation is one of the most valuable everyday Linux skills.

---

> 💡 **Researcher's Note**
>
> Don't try to memorize every path. Instead, understand how the Linux directory tree is organized. Once you know the structure, finding files becomes much more intuitive.

---

# 📍 Current Working Directory

Whenever you open a terminal, you are located inside a directory.

This location is called your **Current Working Directory (CWD).**

To display it, use:

```bash
pwd
```

Example output:

```text
/home/name
```

The `pwd` command stands for:

**Print Working Directory**

---

# 📂 Listing Directory Contents

To see the files and directories stored in your current location, use:

```bash
ls
```

Example:

```text
Documents
Downloads
Pictures
Desktop
Music
```

---

## Useful Options

Show detailed information:

```bash
ls -l
```

Show hidden files:

```bash
ls -a
```

Show detailed information including hidden files:

```bash
ls -la
```

---

# 📁 Changing Directories

To move between directories, use:

```bash
cd
```

Example:

```bash
cd Documents
```

Move into another directory:

```bash
cd Downloads
```

---

#  Return to Your Home Directory

No matter where you are, you can always return home:

```bash
cd ~
```

or simply:

```bash
cd
```

---

# ⬆️ Move to the Parent Directory

To move one directory upward:

```bash
cd ..
```

Example:

```text
/home/name/Documents
```

becomes

```text
/home/name
```

---

# 📍 Absolute Paths

An **absolute path** always begins from the root directory.

Example:

```text
/home/name/Documents/Linux
```

Absolute paths always start with:

```text
/
```

No matter where you currently are, this path always points to the same location.

---

# 📍 Relative Paths

A **relative path** starts from your current directory.

Suppose you are here:

```text
/home/name
```

To enter Documents:

```bash
cd Documents
```

Notice that you didn't type:

```text
/home/name/Documents
```

because Linux already knows your current location.

---

# 🆚 Absolute vs Relative Paths

| Absolute Path                      | Relative Path                 |
| ---------------------------------- | ----------------------------- |
| Starts from `/`                    | Starts from current directory |
| Always points to the same location | Depends on current location   |
| Longer                             | Usually shorter               |

Example:

Absolute:

```text
/home/name/Documents
```

Relative:

```text
Documents
```

---

# 📂 Hidden Files

Linux hides files whose names begin with a period (`.`).

Examples:

```text
.bashrc
.profile
.gitignore
```

To display hidden files:

```bash
ls -a
```

Many hidden files store user preferences and application configuration.

---

#  Useful Directory Shortcuts

| Shortcut | Meaning           |
| -------- | ----------------- |
| `.`      | Current directory |
| `..`     | Parent directory  |
| `~`      | Home directory    |
| `/`      | Root directory    |

Examples:

```bash
cd .
```

Stay in the current directory.

```bash
cd ..
```

Move up one level.

```bash
cd ~
```

Return home.

---

#  Tab Completion

Linux can automatically complete commands and file names.

Example:

Instead of typing:

```text
Documents
```

Type:

```text
Doc
```

then press:

```text
Tab
```

Linux will complete the name if it is unique.

Tab completion:

* Saves time.
* Reduces typing mistakes.
* Makes working in the terminal much faster.

Professional Linux users rely on it constantly.

---

# ❌ Common Misconceptions

### "`cd` creates directories."

No.

It only changes your current location.

---

### "`pwd` changes directories."

No.

It only displays your current directory.

---

### "Hidden files are encrypted."

No.

Hidden files simply begin with a period (`.`).

They are hidden to reduce clutter—not to provide security.

---

### "Absolute paths are always better."

Not necessarily.

Relative paths are often shorter and easier to use when working inside a project.

---

#  Quick Practice

Without using the internet:

1. Display your current working directory.
2. List all files.
3. List hidden files.
4. Return to your home directory.
5. Move one directory upward.
6. Explain the difference between an absolute and a relative path.

If you can complete these tasks, you've mastered the fundamentals of Linux navigation.

---

# ✅ Best Practices

✔ Know your current location before running commands.

✔ Prefer tab completion instead of typing long names.

✔ Use relative paths when working inside projects.

✔ Use absolute paths when writing scripts.

✔ Be careful when navigating system directories.

---

# 📝 Summary

In this lesson you learned:

* What the current working directory is.
* How to display your location using `pwd`.
* How to list files using `ls`.
* How to change directories using `cd`.
* The difference between absolute and relative paths.
* How to work with hidden files.
* Linux directory shortcuts.
* How to use tab completion.

---

#  What's Next?

Now that you can navigate the Linux file system, it's time to start creating and managing files.

In the next lesson, you'll learn how to:

* Create files.
* Create directories.
* Copy files.
* Move files.
* Rename files.
* Delete files safely.

---

## 🧭 Navigation

⬅️ **Previous Lesson**

[Lesson 04 – Linux File System](04-Linux-File-system.md)

➡️ **Next Lesson**

[Lesson 06 – Creating and Managing Files](06-Creating-and-Managing-files.md)

💻 **Lab**

[Lab 05 – Navigating Files and Directories](../../labs/module-01/lab-05-Files-and-Directories)

📄 **Cheat Sheet**

[Files and Directories Cheat Sheet](../../Cheatsheet/module-01)

🛠️ **Troubleshooting**

Module 01 Troubleshooting

📚 **Resources**

[Files and Directories Resources](README.md)
