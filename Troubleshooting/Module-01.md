# 🛠 Module 01 – Troubleshooting

Welcome to the Module 01 Troubleshooting Guide.

This document covers the most common issues beginners encounter while completing the Linux Foundations module.

Before searching online, check the solutions below. Most problems have a simple explanation.

---

#  Before You Begin

Before assuming something is broken, verify the following:

* ✅ Did you type the command exactly as shown?
* ✅ Are you in the correct directory? (`pwd`)
* ✅ Is the filename spelled correctly?
* ✅ Remember that Linux is **case-sensitive**.
* ✅ Read the entire error message carefully.
* ✅ Restart the terminal or virtual machine if something behaves unexpectedly.

---

# 🖥 Installation Problems

## Ubuntu Won't Boot

### Possible Causes

* Virtualization is disabled.
* The ISO file is corrupted.
* The virtual machine does not have enough memory.

### Solution

* Enable Intel VT-x or AMD-V in your BIOS/UEFI.
* Verify that the Ubuntu ISO downloaded correctly.
* Allocate at least **4 GB of RAM** to the virtual machine.
* Restart the virtual machine.

---

## Black Screen After Installation

### Possible Causes

* Graphics compatibility issue.
* Guest Additions or VMware Tools are not installed.
* Virtual machine display settings are incorrect.

### Solution

* Restart the virtual machine.
* Install Guest Additions (VirtualBox) or VMware Tools.
* Increase the video memory in your VM settings.

---

## No Internet Connection

### Possible Causes

* Incorrect network adapter settings.
* Network service is disabled.

### Solution

Verify that the network adapter is enabled.

Restart the virtual machine and reconnect.

---

## Copy & Paste Doesn't Work

### Possible Causes

* Guest Additions (VirtualBox) or VMware Tools are missing.

### Solution

Install Guest Additions or VMware Tools and restart the virtual machine.

---

# 💻 Terminal Problems

## "command not found"

### Possible Causes

* Typo in the command.
* The command is not installed.

### Solution

Check your spelling.

Use:

```bash
which command_name
```

or

```bash
command_name --help
```

if the command exists.

---

## Permission Denied

### Possible Causes

* You do not have permission to access the file.
* The file is not executable.

### Solution

For Module 01, work inside your home directory.

File permissions are covered in a later module.

---

## Terminal Closed Accidentally

### Solution

Simply reopen the Terminal application.

No work is lost unless you had unsaved changes.

---

## I Don't Know Where I Am

### Solution

Run:

```bash
pwd
```

to display your current directory.

---

# 📁 Navigation Problems

## No Such File or Directory

### Possible Causes

* Typo in the filename.
* Wrong directory.
* Incorrect capitalization.

### Solution

Check:

```bash
pwd
```

then

```bash
ls
```

to verify your location and available files.

---

## Hidden Files Don't Appear

### Solution

Display hidden files with:

```bash
ls -a
```

---

## I Can't Find My Files

### Solution

Navigate back to your home directory:

```bash
cd ~
```

Then list your files:

```bash
ls
```

---

## Absolute vs Relative Paths

### Remember

Absolute path:

```text
/home/username/Documents
```

Relative path:

```text
Documents
```

Use the appropriate one depending on your current location.

---

# 📄 File & Directory Problems

## Directory Not Empty

### Problem

Linux refuses to remove a directory.

### Solution

A directory must be empty before using:

```bash
rmdir
```

Otherwise, remove it recursively:

```bash
rm -r directory_name
```

⚠️ Use recursive deletion carefully.

---

## "cp: omitting directory"

### Problem

You're trying to copy a directory without recursion.

### Solution

Use:

```bash
cp -r source_directory destination_directory
```

---

## I Accidentally Deleted a File

### Important

Unlike Windows, the `rm` command does **not** send files to a recycle bin.

Deleted files are often difficult or impossible to recover.

### Prevention

Always verify the filename before pressing Enter.

---

## I Renamed the Wrong File

### Solution

Rename it again using:

```bash
mv old_name new_name
```

---

## I Can't Create a File or Folder

### Possible Causes

* Incorrect location.
* Insufficient permissions.

### Solution

Verify your current directory using:

```bash
pwd
```

Work inside your home directory whenever possible.

---

# ✏ Nano Problems

## I Can't Save My File

Press:

```text
Ctrl + O
```

Then press:

```text
Enter
```

to confirm the filename.

---

## I Can't Exit Nano

Press:

```text
Ctrl + X
```

If Nano asks whether to save your changes, choose the appropriate option.

---

## My Changes Disappeared

The file was probably closed without saving.

Always save before exiting Nano.

---

# ⚡ Vim Problems

## I Can't Type

Vim starts in **Normal Mode**.

Press:

```text
i
```

to enter **Insert Mode**.

---

## I Can't Exit Vim

Press:

```text
Esc
```

Then type:

```text
:q
```

and press Enter.

---

## Save and Exit Vim

Press:

```text
Esc
```

Then type:

```text
:wq
```

Press Enter.

---

## Exit Without Saving

Press:

```text
Esc
```

Then type:

```text
:q!
```

Press Enter.

---

# ❓ Frequently Asked Questions

## Is Linux Case-Sensitive?

Yes.

For example:

```text
Documents
```

and

```text
documents
```

are different directories.

---

## Why Do Some Files Start With a Dot?

Files beginning with `.` are hidden configuration files.

Examples:

```text
.bashrc
.profile
```

---

## Do I Need to Memorize Every Command?

No.

Focus on understanding what each command does.

With practice, the most common commands will become natural.

---

## Can I Damage My Computer?

If you're working inside a virtual machine and following this course, the risk is very low.

If something goes wrong, you can usually restore or reinstall the virtual machine.

---

# 🆘 Need More Help?

If your issue isn't covered here:

1. Review the related lesson.
2. Check the official documentation.
3. Search the repository Discussions.
4. Open a new Discussion and include:

   * Your operating system.
   * The command you ran.
   * The exact error message.
   * A screenshot, if possible.

Providing detailed information makes it much easier for others to help.

---

# 🎉 Congratulations!

If you've completed Module 01 and can solve most of the issues listed above, you've built a solid foundation in Linux.

You're now ready to continue your journey into more advanced Linux concepts and cybersecurity.
