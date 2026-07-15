# 📄 Module 01 – Linux Foundations Cheat Sheet

This cheat sheet summarizes the essential Linux commands and concepts covered in Module 01.

Use it as a quick reference while completing the lessons and labs.

---

# 💻 Terminal Basics

| Command          | Description                         |
| ---------------- | ----------------------------------- |
| `pwd`            | Print the current working directory |
| `whoami`         | Display the current user            |
| `echo`           | Print text to the terminal          |
| `date`           | Display the current date and time   |
| `history`        | Show previously executed commands   |
| `clear`          | Clear the terminal screen           |
| `man command`    | Open the command manual             |
| `command --help` | Display command help                |

---

# 📁 Navigation

| Command        | Description                             |
| -------------- | --------------------------------------- |
| `ls`           | List directory contents                 |
| `ls -l`        | Detailed directory listing              |
| `ls -a`        | Show hidden files                       |
| `ls -la`       | Detailed listing including hidden files |
| `cd directory` | Change directory                        |
| `cd ..`        | Move to the parent directory            |
| `cd ~`         | Return to your home directory           |
| `cd /`         | Go to the root directory                |

---

# 📂 File & Directory Management

| Command                     | Description                    |
| --------------------------- | ------------------------------ |
| `touch file.txt`            | Create an empty file           |
| `mkdir folder`              | Create a directory             |
| `mkdir -p folder/subfolder` | Create nested directories      |
| `cp file backup/`           | Copy a file                    |
| `cp -r folder backup/`      | Copy a directory               |
| `mv old new`                | Move or rename a file          |
| `rm file.txt`               | Delete a file                  |
| `rm -r folder`              | Delete a directory recursively |
| `rmdir folder`              | Delete an empty directory      |
| `cat file.txt`              | Display a file                 |
| `less file.txt`             | View a large file page by page |

---

# ✏ Nano

| Shortcut   | Action                  |
| ---------- | ----------------------- |
| `Ctrl + O` | Save file               |
| `Ctrl + X` | Exit Nano               |
| `Ctrl + W` | Search                  |
| `Ctrl + K` | Cut current line        |
| `Ctrl + U` | Paste                   |
| `Ctrl + C` | Display cursor position |

---

# ⚡ Vim

| Command | Action                |
| ------- | --------------------- |
| `i`     | Enter Insert Mode     |
| `Esc`   | Return to Normal Mode |
| `:w`    | Save                  |
| `:q`    | Quit                  |
| `:wq`   | Save and Quit         |
| `:q!`   | Quit without saving   |

---

# 📂 Important Linux Directories

| Directory | Purpose                              |
| --------- | ------------------------------------ |
| `/`       | Root of the file system              |
| `/home`   | User home directories                |
| `/etc`    | System configuration files           |
| `/usr`    | Installed applications and utilities |
| `/var`    | Logs and variable data               |
| `/tmp`    | Temporary files                      |
| `/boot`   | Bootloader and kernel files          |
| `/dev`    | Device files                         |
| `/proc`   | Process and kernel information       |

---

# ⭐ Wildcards

| Symbol | Meaning                         |
| ------ | ------------------------------- |
| `*`    | Matches zero or more characters |
| `?`    | Matches exactly one character   |

Example:

```bash
ls *.txt
```

Displays every file ending with `.txt`.

---

# ⚠ Common Error Messages

| Error                       | Meaning                                |
| --------------------------- | -------------------------------------- |
| `command not found`         | Command is misspelled or not installed |
| `Permission denied`         | You don't have permission              |
| `No such file or directory` | File or directory doesn't exist        |
| `Directory not empty`       | The directory still contains files     |
| `cp: omitting directory`    | Use `cp -r` to copy directories        |

---

# ⌨ Useful Keyboard Shortcuts

| Shortcut   | Action                                |
| ---------- | ------------------------------------- |
| `Tab`      | Auto-complete commands and file names |
| `↑`        | Previous command                      |
| `↓`        | Next command                          |
| `Ctrl + C` | Stop the running command              |
| `Ctrl + L` | Clear the terminal                    |
| `Ctrl + D` | Exit the current shell                |

---

# 💡 Best Practices

* Use meaningful file and directory names.
* Avoid spaces in filenames; use hyphens (`-`) or underscores (`_`) instead.
* Double-check commands before pressing **Enter**.
* Create backups before deleting or modifying important files.
* Read error messages carefully—they often explain the problem.
* Practice commands regularly to build confidence.

---

# 📝 Module 01 Summary

By completing Module 01, you have learned how to:

* Install Linux.
* Use the terminal.
* Navigate the Linux file system.
* Create and manage files and directories.
* Edit files using Nano and Vim.
* Understand the Linux directory hierarchy.
* Read command documentation.
* Build the foundation required for Linux administration and cybersecurity.

Congratulations on completing your Linux Foundations journey!
