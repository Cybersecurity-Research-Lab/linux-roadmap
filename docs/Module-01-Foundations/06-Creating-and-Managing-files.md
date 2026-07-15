#  Lesson 06 – Creating and Managing Files

> Learn how to create, organize, copy, move, rename, and delete files and directories in Linux using the command line.

---

#  Learning Outcomes

After completing this lesson, you will be able to:

* Create files from the terminal.
* Create directories.
* Copy files and directories.
* Move files between directories.
* Rename files and directories.
* Delete files and directories safely.
* View file contents.
* Understand Linux file naming conventions.
* Use wildcards for efficient file management.
* Explain why proper file management is important in Linux and cybersecurity.

---

## ⏱️ Estimated Study Time

**75–90 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Prerequisites

* [Lesson 01 – Introduction to Linux](01-Introduction-to-Linux.md)
* [Lesson 02 – Installing Linux](02-Installing-Linux.md)
* [Lesson 03 – Terminal Basics](03-Terminal-Basics.md)
* [Lesson 04 – Linux File System](04-Linux-File-system.md)
* [Lesson 05 – Files and Directories](05-Files-and-Directories.md)

---

#  Key Terms

| Term      | Definition                                                    |
| --------- | ------------------------------------------------------------- |
| File      | A container used to store information.                        |
| Directory | A folder used to organize files.                              |
| Wildcard  | A special character used to match multiple files.             |
| Rename    | Changing the name of a file or directory.                     |
| Copy      | Creating a duplicate while keeping the original.              |
| Move      | Changing a file's location.                                   |
| Recursive | Performing an action on a directory and everything inside it. |

---

#  Introduction

Linux provides powerful tools for managing files and directories directly from the terminal.

Instead of dragging files with a mouse, Linux users rely on commands that are faster, more flexible, and easier to automate.

Whether you're writing scripts, managing servers, organizing projects, or performing cybersecurity investigations, you'll constantly create, copy, move, rename, and delete files.

Learning these commands is one of the biggest steps toward becoming comfortable with Linux.

---

#  Why This Matters

Every Linux user manages files.

Developers organize projects.

System administrators manage configuration files.

Cloud engineers deploy applications.

Cybersecurity professionals collect evidence, preserve logs, organize malware samples, and maintain investigation data.

Efficient file management improves productivity and reduces mistakes.

---

#  Cybersecurity Insight

Imagine you're investigating a compromised Linux server.

Before analyzing suspicious files, you should:

* Create an investigation directory.
* Copy suspicious files without modifying the originals.
* Rename files using a case identifier.
* Organize evidence into separate folders.
* Preserve the original timestamps whenever possible.

Poor file management can accidentally destroy valuable forensic evidence.

Understanding Linux file commands helps security professionals maintain the integrity of investigations.

---

> 💡 **Researcher's Note**
>
> One of the most common beginner mistakes is deleting or moving files before creating backups. Develop the habit of making a copy before modifying important files.

---

# 💼 Real-World Example

A SOC analyst discovers a suspicious script inside a user's home directory.

Instead of opening or editing it immediately, they:

1. Create an investigation folder.
2. Copy the file into that folder.
3. Rename it with the incident number.
4. Perform analysis on the copied version.

This protects the original evidence while allowing safe investigation.

---

# 📄 Creating Empty Files

The easiest way to create an empty file is with:

```bash
touch notes.txt
```

If the file doesn't exist, Linux creates it.

If the file already exists, Linux updates its timestamp without changing its contents.

Example:

```bash
touch report.txt
```

---

# 📁 Creating Directories

Create a directory using:

```bash
mkdir Projects
```

Result:

```text
Projects/
```

---

## Creating Multiple Directories

```bash
mkdir Documents Downloads Pictures
```

Linux creates all three directories with a single command.

---

## Creating Nested Directories

Instead of creating one directory at a time:

```bash
mkdir -p Projects/Linux/Labs
```

The `-p` option automatically creates missing parent directories.

Result:

```text
Projects/
└── Linux/
    └── Labs/
```

---

# 📄 Copying Files

Use:

```bash
cp
```

Example:

```bash
cp report.txt backup.txt
```

Result:

```text
report.txt
backup.txt
```

The original file remains unchanged.

---

# 📂 Copying Directories

Directories require the recursive option.

```bash
cp -r Projects Projects_Backup
```

The `-r` option copies the directory and everything inside it.

Without `-r`, Linux will refuse to copy directories.

---

#  Moving Files

Move a file:

```bash
mv report.txt Documents/
```

The file is removed from its original location and placed inside the destination directory.

---

#  Renaming Files

Linux uses the same command.

Example:

```bash
mv report.txt final-report.txt
```

The file is renamed.

No duplicate is created.

---

# 📁 Renaming Directories

Exactly the same command.

```bash
mv OldProjects NewProjects
```

---

# 🗑️ Deleting Files

Remove a file:

```bash
rm report.txt
```

Once deleted, the file does **not** go to a recycle bin.

Be careful.

---

# 🗑️ Deleting Empty Directories

```bash
rmdir EmptyFolder
```

This only works if the directory is empty.

---

# 🗑️ Deleting Non-Empty Directories

```bash
rm -r Projects
```

The `-r` option removes the directory and everything inside it.

Use this command carefully.

---

#  Viewing File Contents

Display the contents of a text file:

```bash
cat notes.txt
```

Example output:

```text
Welcome to Linux!
```

`cat` is commonly used for quickly viewing small text files.

---

# 📑 Viewing Long Files

For longer files, use:

```bash
less notes.txt
```

Advantages:

* Scroll up and down.
* Search inside the file.
* Quit by pressing:

```text
q
```

Many Linux administrators prefer `less` over `cat` for large configuration files.

---

# 📂 File Naming Best Practices

Good examples:

```text
linux-notes.txt
server_backup.sh
incident_report.pdf
```

Avoid:

```text
My File Final Version New (3).txt
```

Use:

* lowercase letters
* hyphens (`-`)
* underscores (`_`)
* meaningful names

Avoid spaces whenever possible.

---

# ⭐ Wildcards

Wildcards allow one command to work with many files.

Match every file:

```text
*
```

Example:

```bash
ls *.txt
```

Displays every text file.

Match one character:

```text
?
```

Example:

```bash
ls file?.txt
```

Matches:

```text
file1.txt
file2.txt
```

but not:

```text
file10.txt
```
---

# ❌ Common Misconceptions

### "`cp` moves a file."

No.

`cp` creates a **copy** while leaving the original file unchanged.

---

### "`mv` only moves files."

No.

`mv` is also used to **rename** files and directories.

Example:

```bash
mv report.txt final-report.txt
```

---

### "`rm` sends files to the recycle bin."

No.

Unlike Windows, `rm` permanently removes files.

Always double-check before pressing **Enter**.

---

### "`mkdir` can only create one directory."

No.

It can create multiple directories at once:

```bash
mkdir Lab1 Lab2 Lab3
```

or nested directories:

```bash
mkdir -p Projects/Linux/Labs
```

---

### "Files with spaces are recommended."

No.

Although Linux supports spaces, they make command-line work more difficult.

Instead of:

```text
My Linux Notes.txt
```

prefer:

```text
my-linux-notes.txt
```

---

# ⚠️ Dangerous Commands

Some Linux commands should always be used carefully.

Delete everything inside a directory:

```bash
rm -r DirectoryName
```

Delete without confirmation:

```bash
rm -rf DirectoryName
```

⚠️ The `-f` (force) option suppresses confirmation prompts.

As a beginner, **avoid using `rm -rf` unless you fully understand what it will delete.**

One incorrect command can remove an entire project or even an operating system.

---

# 💼 Real-World Workflow

Suppose you're organizing a cybersecurity investigation.

Step 1 — Create a directory.

```bash
mkdir Incident-001
```

Step 2 — Copy suspicious files.

```bash
cp suspicious.sh Incident-001/
```

Step 3 — Rename the evidence.

```bash
mv suspicious.sh malware-sample.sh
```

Step 4 — View its contents.

```bash
cat malware-sample.sh
```

Step 5 — Archive or organize additional evidence.

This simple workflow demonstrates how the commands you've learned are used together in real-world scenarios.

---

#  Quick Practice

Complete the following tasks in your Linux virtual machine.

1. Create a directory named:

```text
LinuxPractice
```

2. Enter the directory.

3. Create three empty files:

```text
notes.txt
commands.txt
practice.txt
```

4. Create another directory named:

```text
Backup
```

5. Copy `notes.txt` into `Backup`.

6. Rename:

```text
commands.txt
```

to

```text
linux-commands.txt
```

7. Display the contents of the directory.

8. Display hidden files.

9. Delete `practice.txt`.

10. Verify the remaining files.

If you can complete every task without looking back at the lesson, you've mastered the fundamentals of Linux file management.

---

# ✅ Best Practices

✔ Create meaningful file names.

✔ Avoid spaces in file names whenever possible.

✔ Keep related files inside dedicated directories.

✔ Create backups before editing important files.

✔ Verify your current directory before deleting files.

✔ Read commands carefully before pressing **Enter**.

✔ Be especially careful when using recursive delete commands.

---

# 📝 Summary

In this lesson you learned how to:

* Create files using `touch`.
* Create directories using `mkdir`.
* Copy files using `cp`.
* Copy directories recursively.
* Move files using `mv`.
* Rename files and directories.
* Delete files using `rm`.
* Delete directories using `rmdir` and `rm -r`.
* View file contents using `cat` and `less`.
* Follow Linux file naming conventions.
* Use wildcards to work with multiple files.

These commands form the foundation of everyday Linux usage and are among the most frequently used commands by developers, system administrators, and cybersecurity professionals.

---

#  What's Next?

Now that you know how to organize files and directories, it's time to understand **who can access them**.

In the next module, you'll learn:

* Linux users and groups.
* File ownership.
* Read, write, and execute permissions.
* Permission notation.
* The `chmod` command.
* The `chown` command.
* Why permissions are critical for Linux security.

---

## 🧭 Navigation

⬅️ **Previous Lesson**

[Lesson 05 – Files and Directories](05-Files-and-Directories.md)


💻 **Lab**

[Lab 06 – Creating and Managing Files](../../labs/lab-06-Creating-and-managing-files.md)

📄 **Cheat Sheet**

[Creating and Managing Files Cheat Sheet](../../cheatsheet/module-01

🛠️ **Troubleshooting**



📚 **Resources**

[Creating and Managing Files Resources](README.md)
