# 📖 Lesson 07 – Text Editors (Nano & Vim Basics)

> Learn how to edit text files directly from the Linux terminal using Nano and Vim, two of the most widely used text editors in Linux.

---

#  Learning Outcomes

By the end of this lesson, you will be able to:

* Understand why terminal text editors are essential.
* Open existing files.
* Create new files.
* Edit file contents.
* Save your changes.
* Exit Nano safely.
* Understand Vim's editing modes.
* Perform basic editing in Vim.
* Know when to use Nano and when to use Vim.
* Explain why terminal editors are important in Linux administration and cybersecurity.

---

## ⏱ Estimated Study Time

**75–90 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Prerequisites

* Lesson 01 – Introduction to Linux
* Lesson 02 – Installing Linux
* Lesson 03 – Terminal Basics
* Lesson 04 – Linux File System
* Lesson 05 – Files and Directories
* Lesson 06 – Creating and Managing Files

---

#  Key Terms

| Term               | Definition                                                |
| ------------------ | --------------------------------------------------------- |
| Text Editor        | Software used to create and modify plain text files.      |
| Nano               | A beginner-friendly terminal text editor.                 |
| Vim                | A powerful terminal text editor used by professionals.    |
| Insert Mode        | Vim mode used for typing text.                            |
| Normal Mode        | Vim mode used for navigation and commands.                |
| Configuration File | A text file that controls application or system settings. |

---

#  Introduction

Linux systems rely heavily on **plain text files**.

Configuration files, scripts, logs, source code, cron jobs, SSH settings, and web server configurations are all stored as text files.

Because Linux servers often run without a graphical interface, administrators edit these files directly from the terminal.

For this reason, learning a terminal text editor is one of the most valuable Linux skills.

---

#  Why This Matters

Almost every Linux administrator uses a terminal editor every day.

Examples include:

* Editing SSH configuration.
* Updating firewall rules.
* Writing Bash scripts.
* Modifying web server settings.
* Creating notes.
* Updating configuration files.
* Writing automation scripts.

Without a text editor, managing a Linux server becomes nearly impossible.

---

#  Cybersecurity Insight

Imagine you're responding to an incident on a remote Linux server.

The server has **no graphical interface**.

You connect through SSH and need to:

* Inspect configuration files.
* Update firewall rules.
* Disable a compromised account.
* Review scripts.
* Edit monitoring configurations.

Everything must be done through a terminal editor.

Knowing Nano or Vim can save valuable time during an incident.

---

> 💡 **Researcher's Note**
>
> Nano is excellent for beginners because its shortcuts are displayed on screen. Vim has a steeper learning curve but is available on almost every Linux system and is preferred by many experienced administrators.

---

# 💼 Real-World Example

A system administrator needs to update the SSH configuration file after discovering an insecure setting.

They connect to the server using SSH and edit:

```text
/etc/ssh/sshd_config
```

using Nano or Vim.

After saving the file, they restart the SSH service.

No graphical interface is involved.

---

#  What Is a Text Editor?

A text editor is a program used to create and edit plain text files.

Examples include:

* Configuration files
* Bash scripts
* Python programs
* HTML files
* Log notes
* Markdown documentation

Unlike Microsoft Word, terminal editors do **not** add formatting such as fonts or colors.

They work only with plain text.

---

#  Nano

Nano is the easiest Linux text editor for beginners.

Open a file:

```bash
nano notes.txt
```

If the file does not exist, Nano creates it automatically.

---

#  Creating a New File

Run:

```bash
nano my-first-note.txt
```

Type:

```text
Welcome to Linux!
```

At this point, the file exists only inside Nano.

It is **not** saved yet.

---

#  Saving a File

To save:

Press:

```text
Ctrl + O
```

Nano will ask for the filename.

Simply press:

```text
Enter
```

The file is now saved.

---

#  Exiting Nano

To exit:

```text
Ctrl + X
```

If there are unsaved changes, Nano will ask whether you want to save them before closing.

---

# 🔍 Searching Inside Nano

Press:

```text
Ctrl + W
```

Type the word you're searching for.

Press:

```text
Enter
```

Nano jumps to the first matching result.

This is useful when working with large configuration files.

---

#  Cancelling an Operation

If you accidentally start an operation, press:

```text
Ctrl + C
```

to cancel it.

---

#  Nano Shortcut Reference

| Shortcut | Action                                           |
| -------- | ------------------------------------------------ |
| Ctrl + O | Save file                                        |
| Ctrl + X | Exit Nano                                        |
| Ctrl + W | Search                                           |
| Ctrl + K | Cut current line                                 |
| Ctrl + U | Paste                                            |
| Ctrl + C | Show cursor position / Cancel certain operations |

---

#  Introducing Vim

Vim is one of the most powerful text editors in Linux.

Unlike Nano, Vim uses **different modes**.

This design makes Vim extremely fast once mastered.

Although it may seem confusing at first, many Linux professionals prefer it because of its efficiency.
