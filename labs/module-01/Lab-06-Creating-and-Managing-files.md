# 🧪 Lab 06 – Creating and Managing Files

> Learn how to create, organize, copy, move, rename, and delete files and directories using the Linux command line.

---

#  Lab Objectives

By the end of this lab, you will be able to:

* Create files and directories.
* Create nested directories.
* Copy files and folders.
* Move files between directories.
* Rename files and folders.
* Delete files safely.
* Delete directories.
* View file contents.
* Use wildcards to work with multiple files.
* Apply good file organization practices.

---

## ⏱ Estimated Time

**60–75 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Related Lesson

**Lesson 06 – Creating and Managing Files**

---

# 🛠 Requirements

* Ubuntu Virtual Machine
* Terminal
* Module 01 completed up to Lesson 06

---

#  Before You Start

Open the terminal and return to your home directory.

All work in this lab should be done inside your home directory.

---

#  Exercise 1 – Create Your Workspace

Create the following structure:

```text
Linux-Lab/
├── Notes/
├── Projects/
├── Backups/
└── Logs/
```

### ✅ Expected Result

Verify your structure using:

```bash
tree Linux-Lab
```

If `tree` is not installed, use:

```bash
ls -R Linux-Lab
```

---

#  Exercise 2 – Create Files

Inside **Notes**, create:

```text
linux.txt
commands.txt
practice.txt
```

Inside **Projects**, create:

```text
project1.txt
project2.txt
```

Verify that every file exists.

---

#  Exercise 3 – Copy Files

Copy:

```text
linux.txt
```

into:

```text
Backups
```

Then verify that:

* the original still exists
* the copied file exists

---

#  Exercise 4 – Rename Files

Rename:

```text
practice.txt
```

to

```text
practice-notes.txt
```

Rename:

```text
project1.txt
```

to

```text
linux-project.txt
```

Verify both changes.

---

#  Exercise 5 – Move Files

Move:

```text
commands.txt
```

from

```text
Notes
```

to

```text
Projects
```

Verify that:

* it disappeared from **Notes**
* it now exists inside **Projects**

---

#  Exercise 6 – Copy an Entire Directory

Create a complete backup of:

```text
Projects
```

inside

```text
Backups
```

Verify that every file was copied successfully.

---

#  Exercise 7 – View File Contents

Add a short sentence to:

```text
linux.txt
```

Example:

```text
Linux is my favorite operating system.
```

Display the file using:

```bash
cat linux.txt
```

---

#  Exercise 8 – Delete Files

Delete:

```text
practice-notes.txt
```

Verify it no longer exists.

---

#  Exercise 9 – Delete Directories

Create:

```text
TestFolder
```

Delete it.

Then create:

```text
OldProjects/
    test.txt
```

Delete the entire directory.

---

#  Exercise 10 – Wildcards

Inside **Projects**, create:

```text
lab1.txt
lab2.txt
lab3.txt
notes.pdf
```

Now display only text files.

Next, display every file beginning with:

```text
lab
```

---

# 🧠 Self-Check

Answer the following without opening the lesson.

1. Which command creates an empty file?
2. Which command creates a directory?
3. Which command copies a file?
4. Which command copies directories?
5. Which command renames a file?
6. Which command moves a file?
7. Which command deletes a file?
8. Which command deletes directories recursively?
9. Which command displays file contents?
10. What does the `*` wildcard represent?

---

# 💡 Mini Challenge

Without using the lesson, complete the following:

Create:

```text
CyberLab/
├── Evidence/
├── Reports/
├── Logs/
└── Backup/
```

Inside **Evidence**, create:

```text
sample1.txt
sample2.txt
sample3.txt
```

Now:

* Copy all evidence into Backup.
* Rename `sample1.txt` to `malware-sample.txt`.
* Move `sample2.txt` into Logs.
* Delete `sample3.txt`.
* Display the contents of `malware-sample.txt`.
* Verify your final directory structure.

---

#  Cybersecurity Challenge

Imagine you're a SOC analyst investigating an incident.

Create the following structure:

```text
Incident-2026-001/
├── Evidence/
├── Reports/
├── Screenshots/
└── Backup/
```

Move evidence into the correct folder.

Create a backup.

Rename files using meaningful names.

Delete unnecessary temporary files.

Think about why preserving organization is important during an investigation.

---

# Screenshot Challenge

Capture screenshots of:

* Your completed directory structure.
* Copying a file.
* Renaming a file.
* Moving a file.
* Displaying a file with `cat`.
* Using a wildcard.
* Final completed workspace.

Save them in:

```text
assets/screenshots/module-01/
```

---

# 🏁 Lab Complete

Congratulations!

You've completed your first real Linux file management project.

You can now confidently create, organize, copy, move, rename, delete, and inspect files from the terminal. These skills are used every day by Linux users, developers, system administrators, cloud engineers, and cybersecurity professionals.

Continue with [Lesson 07 -Text Editors (Nano & Vim Basics)](../../docs/Module-01-Foundations/07-Text-Editors.md)
