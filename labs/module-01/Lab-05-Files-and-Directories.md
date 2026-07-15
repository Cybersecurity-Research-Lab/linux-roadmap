# 🧪 Lab 05 – Navigating Files and Directories

> Practice navigating the Linux file system using the terminal and become comfortable moving between directories using absolute and relative paths.

---

# Lab Objectives

By the end of this lab, you will be able to:

* Display your current working directory.
* Navigate between directories.
* Use absolute and relative paths.
* Return to your home directory.
* Move to parent directories.
* Display hidden files.
* Use tab completion.
* Understand common navigation shortcuts.

---

## ⏱ Estimated Time

**45–60 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Related Lesson

**Lesson 05 – Files and Directories**

---

# 🛠 Requirements

* Ubuntu Virtual Machine
* Terminal
* Module 01 completed up to Lesson 05

---

#  Before You Start

Open the terminal.

Make sure you're inside your home directory before starting.

---

#  Exercise 1 – Where Am I?

Display your current working directory.

### ✅ Expected Result

You should see something similar to:

```text
/home/username
```

---

#  Exercise 2 – List Files

Display the contents of your current directory.

Then display:

* detailed information
* hidden files
* detailed information including hidden files

### ✅ Expected Result

You should be able to identify the difference between:

* normal listing
* detailed listing
* hidden files

---

#  Exercise 3 – Enter a Directory

Move into one of these directories if it exists:

```text
Documents
Downloads
Desktop
Pictures
```

Verify your location.

### ✅ Expected Result

Example:

```text
/home/username/Documents
```

---

#  Exercise 4 – Return Home

Without typing the complete path, return to your home directory.

### ✅ Expected Result

```text
/home/username
```

---

#  Exercise 5 – Parent Directory

Move one level upward.

Verify your location.

Return to your home directory afterwards.

---

#  Exercise 6 – Absolute Path

Using an **absolute path**, navigate directly into:

```text
/home/username
```

Replace **username** with your own Linux username.

---

#  Exercise 7 – Relative Path

Suppose you're already inside your home directory.

Navigate into one of your folders using only a **relative path**.

### Example

```text
Documents
```

Do **not** type:

```text
/home/username/Documents
```

---

#  Exercise 8 – Hidden Files

Display hidden files.

Identify at least two hidden files.

Examples might include:

```text
.bashrc
.profile
.cache
```

---

#  Exercise 9 – Tab Completion

Start typing the name of a directory.

Example:

```text
Doc
```

Instead of finishing the word, press:

**Tab**

Linux should complete the directory name automatically.

Repeat with another directory.

---

#  Exercise 10 – Navigation Shortcuts

Complete the following tasks:

* Return to the current directory.
* Move to the parent directory.
* Return home.
* Return to the root directory.

Verify your location after every command.

---

# 🧠 Self-Check

Answer the following without opening the lesson.

1. What command displays your current location?
2. What command changes directories?
3. What command lists directory contents?
4. What is the difference between an absolute path and a relative path?
5. What does `~` represent?
6. What does `..` represent?
7. What command displays hidden files?

---

# 💡 Mini Challenge

Complete the following without using the lesson.

1. Go to your home directory.
2. Enter the Documents folder.
3. Return to the parent directory.
4. Enter another folder.
5. Display hidden files.
6. Return to the root directory.
7. Return to your home directory using the shortest possible command.

If you completed every task without searching online, you've mastered Linux navigation.

---

# 🛡 Cybersecurity Challenge

You're investigating a Linux server.

Your manager asks you to:

* Navigate to the user's home directory.
* Locate hidden configuration files.
* Return to the root directory.
* Move into the log directory.
* Return to your own home directory.

Complete the entire workflow **without using the graphical interface**.

---

#  Screenshot Challenge

Take screenshots of:

* Output of your current working directory.
* Hidden files displayed.
* Root directory listing.
* Home directory listing.
* A successful use of tab completion (before and after).

Save them inside:

```text
assets/screenshots/module-01/
```

---

# 🏁 Lab Complete

Congratulations!

You can now confidently navigate the Linux file system using the terminal.

Navigation is one of the most frequently used Linux skills and forms the foundation for system administration, scripting, and cybersecurity.

Continue with [Lesson 06 – Creating and Managing Files](../../docs/Module-01-Foundations/Creating-and-Managing-files.md)
