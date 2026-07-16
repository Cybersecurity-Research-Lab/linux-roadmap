🧪 Hands-On Lab: Linux Networking Basics

Objective: Build fluency reading a host's real network configuration and reasoning about exposure.

Tasks:


Run ip addr show on your lab system and record every interface present, its IP address(es), and CIDR prefix.
Run ip route show and identify the default gateway and any additional routes present.
Calculate, by hand, how many usable host addresses exist in your primary interface's subnet based on its CIDR prefix, then verify your math with an online or manual subnet calculation.
Start a simple test service bound to 127.0.0.1 only (e.g., python3 -m http.server 8080 --bind 127.0.0.1), and confirm from the same machine that curl http://127.0.0.1:8080 works.
If you have a second machine or VM on the same network, attempt to reach the service from there using the host's real IP address and confirm it fails (because it's bound only to loopback) — if you only have one machine, explain in writing why you'd expect this to fail.
Restart the same test service bound to 0.0.0.0 instead, and repeat the reachability test from the second machine — confirm it now succeeds, and explain the security implication in 2–3 sentences.
Identify which of your interfaces is the loopback interface and confirm its address is within the 127.0.0.0/8 range.


Deliverable: A short log of your commands and observations, plus your 2–3 sentence explanation from Task 6.


Navigation

➡️[ Next: Lesson 02 – SSH](../../docs/Module-03-Linux-Networking/02-ssh.md)
