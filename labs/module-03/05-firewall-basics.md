🧪 Hands-On Lab: Firewall Basics

Objective: Build a default-deny firewall policy safely on a lab system, without locking yourself out.

Tasks:


Identify which firewall tool is available/active on your lab system (ufw, firewalld, or raw iptables/nftables) and check its current status and default policy.
Before making any changes, confirm you have an alternate access path to this system (console access, or accept that a lockout means rebuilding the VM) — do not skip this step.
If using ufw: set default deny incoming, default allow outgoing, then explicitly allow SSH (port 22) — in that order, checking status after each step.
If using firewalld: review the active zone, add a permanent SSH service rule, and reload.
Enable the firewall (ufw enable or confirm firewalld/iptables policy is active) and confirm, from a new SSH session (keep your original open), that you can still connect.
Start a simple test service on a non-standard port (e.g., python3 -m http.server 8888) and confirm from Lesson 03's tools (ss -tulnp, then curl from the same machine) that it's running but not yet allowed through the firewall from elsewhere.
Add a specific allow rule for that port, verify the change takes effect (test with curl from a second machine, or explain why you can't test that here if using a single-VM setup), then remove the rule again and confirm it's blocked once more.
List the complete current rule set with the appropriate command for your tool and save the output as your lab record.
Write 2–3 sentences describing a scenario, based on this lab, where forgetting to persist firewall rules (or forgetting --permanent) could create a false sense of security.


Deliverable: Your final rule set output, plus the written scenario from Task 9.

