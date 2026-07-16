🧪 Hands-On Lab: Package Managers

Objective: Practice installing, auditing, and removing software safely.

Tasks:


Identify whether your lab system is Debian-based or Red Hat-based (cat /etc/os-release).
Refresh the package index (apt update or dnf check-update).
Install a small, harmless utility you don't already have (e.g., tree or htop).
Confirm it installed correctly using the query command for your package manager (dpkg -l or rpm -qa), and list every file it installed.
Check how many packages on your system currently have available upgrades.
Review your configured repositories (/etc/apt/sources.list* or /etc/yum.repos.d/) and list each one you find, noting whether it's an official distro repository or a third party.
Find which package owns a common binary on your system (e.g., /bin/bash or /usr/bin/python3) using dpkg -S or rpm -qf.
Remove the utility you installed in step 3, then confirm removal, then check whether any configuration files were left behind.
(Discussion, no command required) Write 2–3 sentences describing what risk you'd flag if you found a third-party repository configured on a production server that nobody on the team recognized.


Deliverable: A short log of your commands/output for each step, plus your step 9 write-up.


Navigation

➡️[ Next: Lesson 06 – Environment Variables](../../docs/Module-02-Working-with-Linux/06-environment-variables.md)
