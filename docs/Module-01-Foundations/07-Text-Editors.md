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

---

# 📝 Understanding Vim Modes

Vim works differently from most text editors because it has multiple modes.

The two most important modes are:

| Mode            | Purpose                                 |
| --------------- | --------------------------------------- |
| **Normal Mode** | Navigate the file and execute commands. |
| **Insert Mode** | Type and edit text.                     |

When Vim starts, it opens in **Normal Mode**.

You cannot immediately begin typing.

---

#  Entering Insert Mode

Open a file:

```bash
vim notes.txt
```

Press:

```text
i
```

You are now in **Insert Mode**.

You can begin typing normally.

---

#  Saving in Vim

First, press:

```text
Esc
```

to return to **Normal Mode**.

Then type:

```text
:w
```

Press:

```text
Enter
```

The file is saved.

---

#  Exiting Vim

Return to **Normal Mode** by pressing:

```text
Esc
```

Quit Vim:

```text
:q
```

Press:

```text
Enter
```

---

#  Save and Exit

The most common command:

```text
:wq
```

Save the file and quit.

---

# 🚫 Exit Without Saving

If you made changes but do **not** want to save them:

```text
:q!
```

This forces Vim to quit without saving.

---

#  Essential Vim Commands

| Command | Action                |
| ------- | --------------------- |
| `i`     | Enter Insert Mode     |
| `Esc`   | Return to Normal Mode |
| `:w`    | Save                  |
| `:q`    | Quit                  |
| `:wq`   | Save and Quit         |
| `:q!`   | Quit Without Saving   |

You do **not** need to memorize advanced Vim commands yet.

Knowing these basics is enough for most beginner Linux tasks.

---

# ⚖️ Nano vs Vim

| Feature                                 | Nano        | Vim              |
| --------------------------------------- | ----------- | ---------------- |
| Beginner Friendly                       | ✅ Yes       | ❌ Learning curve |
| Available on most Linux systems         | ✅ Usually   | ✅ Almost always  |
| Displays shortcuts                      | ✅ Yes       | ❌ No             |
| Best for quick edits                    | ✅ Excellent | ✅ Excellent      |
| Preferred by experienced administrators | Sometimes   | Very often       |

For beginners:

* Learn **Nano** first.
* Become familiar with **basic Vim** commands.
* Master Vim gradually as your Linux skills grow.

---

#  Cybersecurity Example

Suppose you're reviewing the SSH configuration of a Linux server.

You connect using SSH and open:

```bash
nano /etc/ssh/sshd_config
```

or

```bash
vim /etc/ssh/sshd_config
```

You inspect the configuration, make the required changes, save the file, and exit.

This is one of the most common tasks performed by Linux administrators and security professionals.

---

# ❌ Common Misconceptions

### "Nano and Vim are programming editors."

No.

They are **general-purpose text editors**.

They can edit:

* Configuration files
* Bash scripts
* Source code
* Notes
* Markdown files
* Log files

---

### "Vim is better than Nano."

Not necessarily.

Nano is easier for beginners.

Vim is more powerful once you become comfortable with it.

Both are valuable tools.

---

### "If I close Nano, my work is automatically saved."

No.

You must explicitly save your changes before exiting.

---

### "Vim is impossible to learn."

No.

Most beginners only need a handful of commands:

* `i`
* `Esc`
* `:w`
* `:q`
* `:wq`
* `:q!`

That's enough to edit configuration files confidently.

---

#  Quick Practice

Without opening the lesson:

1. Create a new file using Nano.
2. Write two sentences.
3. Save the file.
4. Exit Nano.
5. Open the same file using Vim.
6. Enter Insert Mode.
7. Add another sentence.
8. Save the file.
9. Exit Vim.
10. Display the file using `cat`.

If you completed every task successfully, you've learned the fundamentals of terminal text editors.

---

# ✅ Best Practices

✔ Learn Nano before learning advanced Vim features.

✔ Always verify that your file has been saved before exiting.

✔ Read configuration files carefully before modifying them.

✔ Keep backup copies of important configuration files.

✔ Practice editing small files before working on production servers.

✔ Remember that many Linux servers only provide terminal-based editors.

---

# 📝 Summary

In this lesson you learned:

* Why Linux uses terminal text editors.
* The purpose of Nano.
* The purpose of Vim.
* How to create and edit files using Nano.
* How to save and exit Nano.
* Vim's Normal and Insert modes.
* How to save and exit Vim.
* When to use Nano and when to use Vim.
* Why text editors are essential for Linux administration and cybersecurity.

---

#  What's Next?

Now that you can create, edit, and manage file it's time to understand **who can access them**.

In the next module, you'll learn:

* Linux users and groups.
* File ownership.
* Read, write, and execute permissions.
* Permission notation.
* The `chmod` command.
* The `chown` command.
* Why permissions are critical for Linux security.

---

## 🧭 Navigation

⬅️ **Previous Lesson**

[Lesson 06 – Creating and Managing Files](06-Creating-and-Managing-files.md)

💻 **Lab**

[Lab 07 – Text Editors (Nano & Vim Basics)](../../labs/module-01/Lab-07-Text-Editors.md)

📄 **Cheat Sheet**

Text Editors Cheat Sheet

🛠️ **Troubleshooting**

Module 01 Troubleshooting

📚 **Resources**

Text Editors Resources
