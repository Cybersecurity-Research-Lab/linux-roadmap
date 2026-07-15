# 🧪 Lab 07 – Text Editors (Nano & Vim Basics)

> Learn how to create, edit, save, and manage text files using Nano and Vim, the two most common terminal text editors in Linux.

---

# 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create text files using Nano.
* Edit existing files.
* Save changes.
* Exit Nano correctly.
* Search inside a file.
* Open and edit files using Vim.
* Understand Vim's Normal Mode and Insert Mode.
* Save and exit Vim.
* Exit Vim without saving.
* Build confidence using terminal-based text editors.

---

## ⏱ Estimated Time

**45–60 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Related Lesson

**Lesson 07 – Text Editors (Nano & Vim Basics)**

---

# 🛠 Requirements

* Ubuntu Virtual Machine
* Terminal
* Nano installed (default on Ubuntu)
* Vim installed (usually pre-installed)

---

#  Before You Start

Open your terminal.

Create a dedicated workspace for this lab.

---

#  Exercise 1 – Create the Lab Folder

Create the following directory:

```text
Editor-Lab/
```

Move into it.

Verify your current working directory.

---

#  Exercise 2 – Create Your First File Using Nano

Open Nano:

```bash
nano notes.txt
```

Type the following:

```text
Welcome to Linux!

Nano is simple and beginner-friendly.

I am learning Linux every day.
```

Do **not** exit yet.

---

#  Exercise 3 – Save the File

Save your work.

### ✅ Expected Result

The file should now exist inside your current directory.

Verify by listing the files.

---

#  Exercise 4 – Exit Nano

Close Nano.

Reopen the file.

Verify that all of your text was saved correctly.

---

#  Exercise 5 – Search Inside Nano

Using Nano's search feature:

Find the word:

```text
Linux
```

Repeat the search for:

```text
beginner
```

---

#  Exercise 6 – Edit the File

Add the following paragraph:

```text
Linux is used by developers, system administrators, cloud engineers, and cybersecurity professionals all over the world.
```

Save your changes.

Exit Nano.

---

#  Exercise 7 – View the File

Display the contents of the file using:

```bash
cat notes.txt
```

Verify that every line appears correctly.

---

#  Exercise 8 – Open the Same File Using Vim

Run:

```bash
vim notes.txt
```

At first, try typing.

###  What happens?

Notice that nothing is inserted.

This is because Vim starts in **Normal Mode**.

---

#  Exercise 9 – Enter Insert Mode

Press:

```text
i
```

Now add:

```text
This line was added using Vim.
```

---

#  Exercise 10 – Save and Exit Vim

Return to **Normal Mode**.

Save the file.

Exit Vim.

Verify your changes using:

```bash
cat notes.txt
```

---

#  Exercise 11 – Exit Without Saving

Create another file:

```bash
vim temporary.txt
```

Enter Insert Mode.

Type:

```text
This file should not be saved.
```

Return to Normal Mode.

Exit **without saving**.

Verify that:

```text
temporary.txt
```

does **not** exist.

---

#  Exercise 12 – Compare Nano and Vim

Complete the table.

| Question                               | Nano | Vim |
| -------------------------------------- | ---- | --- |
| Easier for beginners                   |      |     |
| Uses editing modes                     |      |     |
| Displays shortcuts                     |      |     |
| Preferred by many advanced Linux users |      |     |

---

# 🧠 Self-Check

Answer the following without opening the lesson.

1. Which command opens Nano?
2. Which command opens Vim?
3. Which shortcut saves a file in Nano?
4. Which shortcut exits Nano?
5. Which key enters Insert Mode in Vim?
6. Which key returns to Normal Mode?
7. Which command saves and exits Vim?
8. Which command exits Vim without saving?

---

# 💡 Mini Challenge

Without looking at the lesson:

1. Create a folder called:

```text
LinuxNotes
```

2. Inside it, create:

```text
commands.txt
```

using Nano.

3. Write three Linux commands you've learned so far.

4. Save and exit Nano.

5. Open the same file using Vim.

6. Add three more commands.

7. Save and exit.

8. Display the file in the terminal.

If you completed all the tasks successfully, you've mastered the basics of terminal text editors.

---

#  Cybersecurity Challenge

Imagine you've connected to a remote Linux server using SSH.

You need to update a configuration file for an investigation.

Create the following file:

```text
incident-notes.txt
```

Write:

* Incident ID
* Date
* Hostname
* Summary
* Analyst Notes

Save the file using Nano.

Reopen it using Vim.

Add one additional observation.

Save the file and verify your changes.

This simulates documenting findings during a real cybersecurity investigation.

---

#  Screenshot Challenge

Capture screenshots of:

* Nano editing a file.
* Nano's shortcut bar at the bottom of the screen.
* Vim in Normal Mode.
* Vim in Insert Mode.
* Your completed `notes.txt` displayed with `cat`.
* Your final `Editor-Lab` directory.

Save them in:

```text
assets/screenshots/module-01/
```

---

# 🏁 Lab Complete

Congratulations!

You've successfully learned how to use Linux's two most popular terminal text editors.

Being comfortable with Nano and understanding the basics of Vim will make it much easier to edit configuration files, write scripts, document investigations, and manage Linux systems throughout the rest of your learning journey.
