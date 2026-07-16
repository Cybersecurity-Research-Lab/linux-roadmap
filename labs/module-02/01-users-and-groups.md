🧪 Hands-On Lab: Users and Groups

Objective: Create a small team structure with appropriate access boundaries.

Setup: A Linux VM/container where you have sudo access.

Tasks:


Create two new users: alice and bob, each with a home directory and /bin/bash shell.
Set a password for each account.
Create a group called project-x.
Add both alice and bob to project-x using the correct (non-destructive) command.
Confirm both users' group memberships using id.
Create a third user, contractor, but configure their shell as /usr/sbin/nologin (simulating a service/restricted account).
Attempt to switch to the contractor account with su - contractor and observe what happens — record the result.
Lock the contractor account with usermod -L and explain, in your own words, what changed versus before.
Audit the system for any account with UID 0 other than root using the awk command from the Researcher's Note.
Clean up: remove alice, bob, and contractor (with home directories), and delete the project-x group.


Deliverable: A short text log of each command you ran and its output, plus one sentence explaining why /usr/sbin/nologin is a safer default for non-human accounts than /bin/bash.

Navigation

➡️ Next: Lesson 02 – File Permissions
