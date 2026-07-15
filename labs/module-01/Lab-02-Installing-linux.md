# 💻 Lab 02 – Installing Ubuntu in a Virtual Machine

> Install Ubuntu Desktop inside a virtual machine and prepare your Linux environment for the rest of this roadmap.

---

## 📌 Lab Information

⏱️ **Estimated Time:** 45–60 minutes

🎯 **Difficulty:** Beginner

🟩 **Lab Type:** Hands-on

📖 **Related Lesson:** [Lesson 02 – Installing Linux](../../docs/Module-01-Foundations/02-Installing-Linux.md)

---

# 🎯 Objectives

By completing this lab, you will be able to:

* Download Ubuntu Desktop.
* Install Oracle VirtualBox.
* Create a virtual machine.
* Install Ubuntu Desktop.
* Verify that your Linux environment is working correctly.

---

# 📋 Requirements

Before starting, make sure you have:

* A computer with virtualization support (Intel VT-x or AMD-V)
* At least **8 GB RAM** (16 GB recommended)
* At least **50 GB** of available storage
* Internet connection
* Administrator privileges

---

# 🚀 Part 1 – Download Ubuntu

1. Visit the official Ubuntu website.
2. Download the latest **Ubuntu Desktop LTS** ISO.
3. Save the ISO somewhere easy to find (such as your Downloads folder).

>  **Ubuntu Download Page**
> ![Ubuntu Download Page](../assets/ubuntu-download-page.png)

> 💡 **Why LTS?**
>
> LTS (Long Term Support) releases receive updates for several years and are recommended for beginners because they are stable and widely supported.

---

## ✅ Checkpoint

* [ ] Ubuntu ISO downloaded successfully.

---

# 🚀 Part 2 – Install Oracle VirtualBox

1. Download VirtualBox from the official website.
2. Run the installer.
3. Keep the default installation settings unless you have a specific reason to change them.
4. Launch VirtualBox.

> **VirtualBox Installer**
> ![VirtualBox Installer](../assets/virtualbox-install-page.png)

> **VirtualBox Home Screen**
> ![VirtualBox Home Screen](../assets/virtualbox-home.png)

---

## ✅ Checkpoint

* [ ] VirtualBox installed successfully.
* [ ] VirtualBox launches without errors.

---

# 🚀 Part 3 – Create a Virtual Machine

Click **New** and configure the virtual machine using the following settings.

| Setting      | Recommended Value     |
| ------------ | --------------------- |
| Name         | Ubuntu-Lab            |
| Type         | Linux                 |
| Version      | Ubuntu (64-bit)       |
| RAM          | 4096 MB               |
| CPU          | 2 Cores               |
| Disk         | 30 GB                 |
| Storage Type | Dynamically Allocated |

>  **Create New Virtual Machine**
   ![Create New Virtual Machine](../assets/create-vm.png)

>  **Virtual Hard Disk Configuration**
    ![Virtual Hard Disk Configuration](../assets/ram-allocation.png)

> 💡 **Why 4 GB RAM?**
>
> Ubuntu runs comfortably with 4 GB while leaving enough memory available for your host operating system.

---

## Attach the Ubuntu ISO

Before starting the VM:

1. Open **Settings**.
2. Navigate to **Storage**.
3. Select the empty optical drive.
4. Attach the Ubuntu ISO.

> **Attach Ubuntu ISO**
> ![Attach Ubuntu ISO](../assets/attach-iso.png)

---

## ✅ Checkpoint

* [ ] Virtual machine created.
* [ ] Ubuntu ISO attached successfully.

---

# 🚀 Part 4 – Install Ubuntu

Start the virtual machine.

Follow the installation wizard.

Recommended choices:

* Language → Your preferred language
* Keyboard Layout → Your keyboard layout
* Installation Type → **Normal Installation**
* Third-Party Software → Enable (optional but recommended)
* Disk Setup → **Erase Disk and Install Ubuntu**

> ⚠️ **Important**
>
> This erases **only the virtual disk**, **not your actual computer**.

Create:

* Username
* Computer name
* Password

Wait until installation finishes.

Restart the virtual machine when prompted.

---

## ✅ Checkpoint

* [ ] Ubuntu installed successfully.
* [ ] Ubuntu restarted correctly.
* [ ] Login screen appears.

---

# 🚀 Part 5 – First Boot

Log into Ubuntu.

Verify:

* Desktop loads correctly.
* Internet connection works.
* Settings application opens.
* Firefox launches.
* Terminal opens successfully.

Open Terminal by pressing Ctrl + Alt + T, or click Activities, search for Terminal, and open it. Then run:

```bash
pwd
whoami
uname -r
ls
```

You don't need to understand every command yet—we'll learn them in the next lessons.

 **Ubuntu Desktop**
 ![Ubuntu Desktop](../assets/ubuntu-desktop.png)

---

## ✅ Checkpoint

* [ ] Terminal opens successfully.
* [ ] All commands execute correctly.

---

# 🚨 Common Problems

## VirtualBox only shows 32-bit operating systems

Possible causes:

* Virtualization disabled in BIOS/UEFI
* Hyper-V enabled (Windows)

---

## Virtual machine does not start

Possible causes:

* Not enough RAM allocated
* Virtualization disabled

---

## Ubuntu boots to a black screen

Try:

* Restarting the VM
* Updating VirtualBox
* Increasing video memory

---

## No Internet Connection

Check:

* VirtualBox Network Adapter
* Host internet connection

---

## Screen Resolution Is Too Small

Install **VirtualBox Guest Additions** after Ubuntu is installed.

---

> 📚 More troubleshooting tips will be available in the dedicated **Troubleshooting** folder.

---

# 🧪 Mini Challenge

Complete one of the following tasks:

⭐ Change the desktop wallpaper.

⭐⭐ Create a VirtualBox snapshot.

⭐⭐⭐ Install a second Linux distribution (Fedora or Debian) and compare the installation process.

---

# 🤔 Reflection

Answer these questions:

1. Which installation step was the easiest?
2. Which step was the most challenging?
3. Why is using a virtual machine recommended for beginners?
4. What did you learn during this installation?

---

# 📝 Researcher's Note

Document everything.

Professional cybersecurity analysts keep detailed notes during investigations. Start building this habit now by recording:

* What you learned
* Problems you encountered
* How you solved them

These notes will become valuable references as you continue learning.

---

# 🆘 Need Help?

If you get stuck:

1. Read the lesson again.
2. Review the **Common Problems** section above.
3. Check the repository's **Troubleshooting** guide.
4. Search the repository **Discussions**.
5. If your issue hasn't been answered, open a new discussion with:

   * Your operating system
   * VirtualBox version
   * Ubuntu version
   * Screenshots of the problem
   * Any error messages

Helping each other improves the roadmap for everyone.

---

# 📸 Lab Completed

Congratulations! 🎉

You have successfully installed Ubuntu in a virtual machine.

Consider taking a screenshot of your completed desktop and saving it as part of your personal learning journal or portfolio.

---

# ✅ Completion Checklist

Before moving on, make sure you can:

* [ ] Download Ubuntu.
* [ ] Install VirtualBox.
* [ ] Create a virtual machine.
* [ ] Install Ubuntu successfully.
* [ ] Boot into Ubuntu.
* [ ] Open the Terminal.
* [ ] Run your first Linux commands.
* [ ] Access the internet.

---

# 📚 Official Documentation

* Ubuntu Documentation
* Oracle VirtualBox Documentation

---

➡️ **Next Lesson**

📖 [Lesson 03 – Terminal Basics](../../docs/Module-01-Foundations/03-Terminal-Basics.md)
