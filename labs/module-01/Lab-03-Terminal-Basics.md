# 🧪 Lab 03 – Terminal Basics

> Become comfortable using the Linux terminal by running your first commands and understanding how the shell responds.

---

#  Lab Objectives

By the end of this lab, you will be able to:

* Open the Linux terminal.
* Execute basic Linux commands.
* Understand command syntax.
* Read terminal output.
* Navigate command history.
* Use tab completion.
* Know where to get help for commands.

---

## ⏱ Estimated Time

**30–45 minutes**

---

## 🎯 Difficulty

🟢 Beginner

---

## 📖 Related Lesson

**Lesson 03 – Terminal Basics**

---

# 🛠 Requirements

* Ubuntu Virtual Machine
* Terminal application
* No Internet required

---

# 📚 Before You Start

Open the terminal.

You should see something similar to:

```text
name@ubuntu:~$
```

Don't worry if your username is different.

---

#  Exercise 1 – Your First Command

Run:

```bash
echo "Hello Linux!"
```

### ✅ Expected Output

```text
Hello Linux!
```

---

#  Exercise 2 – Who Am I?

Run:

```bash
whoami
```

### ✅ Expected Output

Your Linux username.

Example:

```text
lynda
```

---

#  Exercise 3 – Where Am I?

Run:

```bash
pwd
```

### ✅ Expected Output

Something similar to:

```text
/home/name
```

---

#  Exercise 4 – Display Today's Date

Run:

```bash
date
```

### ✅ Expected Output

Current date and time.

---

#  Exercise 5 – Display the Calendar

Run:

```bash
cal
```

If you receive:

```text
Command 'cal' not found
```

that's completely normal on some Ubuntu installations.

You can skip this exercise or install the package later.

---

#  Exercise 6 – Clear the Screen

Run:

```bash
clear
```

### ✅ Expected Result

The terminal becomes empty while keeping your prompt at the top.

---

#  Exercise 7 – Command History

Run:

```bash
history
```

### ✅ Expected Result

You should see a numbered list of commands you've already executed.

Example:

```text
1 echo "Hello Linux!"
2 whoami
3 pwd
4 date
```

---

#  Exercise 8 – Use the Arrow Keys

Press:

⬆️ Up Arrow

The previous command should appear.

Press Enter to execute it again.

---

#  Exercise 9 – Tab Completion

Type only:

```text
his
```

Do **not** press Enter.

Instead, press:

**Tab**

Linux should complete the command to:

```text
history
```

Now press Enter.

---

# 🚀 Exercise 10 – Get Help

Run:

```bash
man pwd
```

### ✅ Expected Result

The manual page for the `pwd` command opens.

Try:

* Arrow Up
* Arrow Down
* Page Up
* Page Down

When finished, press:

```text
q
```

to quit.

---

#  Exercise 11 – Exit the Terminal

Run:

```bash
exit
```

Then open the terminal again.

---

# 🧠 Self-Check

Can you answer these questions without searching online?

1. What command displays your current directory?
2. What command prints text to the screen?
3. How do you clear the terminal?
4. Which key recalls previous commands?
5. What command displays the manual page of another command?
6. How do you leave the manual page?
7. What command closes the terminal session?

---

# 💡 Mini Challenge

Without looking back at the lesson, perform the following:

* Print "Linux is awesome!"
* Display your username.
* Display today's date.
* Show your command history.
* Open the manual page for `date`.
* Exit the manual.
* Clear the screen.

If you completed all seven tasks successfully, you've mastered the basics of the Linux terminal.

---

# 🏁 Lab Complete

Congratulations!

You've successfully completed your first hands-on Linux terminal lab.

You're now comfortable opening the terminal, running commands, reading output, using command history, accessing documentation, and navigating the shell.

Continue with [Lesson 04 – Linux File System](../../docs/Module-01-Foundations/04-Linux-File-system.md)
