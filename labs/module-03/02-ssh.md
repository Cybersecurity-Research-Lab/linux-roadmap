🧪 Hands-On Lab: SSH

Objective: Set up key-based authentication end to end and apply core SSH hardening.

Tasks:


Generate a new ed25519 SSH key pair specifically for this lab.
Deploy the public key to a lab server (or a second local user account, if you only have one machine) using ssh-copy-id.
Confirm you can log in using the key with no password prompt, and confirm the private key file has 600 permissions.
Deliberately loosen the permissions on the private key (chmod 644) and attempt to connect again — record what happens, then restore correct permissions.
Edit /etc/ssh/sshd_config (on a lab/disposable system only) to set PasswordAuthentication no and PermitRootLogin no. Run sudo sshd -t to validate syntax before restarting.
Restart sshd, keeping your current session open, and confirm in a new terminal window that key-based login still works and password login is now refused.
Create a ~/.ssh/config entry for your lab host and confirm ssh <alias> connects correctly using the shortened syntax.
If available, install and enable fail2ban, then check its status for the SSH jail with fail2ban-client status sshd.
Review /var/log/auth.log (or journalctl -u ssh) and identify the log lines corresponding to your successful and any failed login attempts from this lab.


Deliverable: Your final sshd_config hardening lines, a screenshot or copied output confirming key-based login works with password auth disabled, and 2–3 sentences describing what you observed in Task 4.


Navigation

 ➡️ [Next: Lesson 03 – Network Utilities](../../docs/Module-03-Linux-Networking/03-network-utilities.md)
