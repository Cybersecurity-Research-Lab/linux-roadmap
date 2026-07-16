🧪 Hands-On Lab: Network Utilities

Objective: Practice the full diagnostic toolkit against real (authorized) targets.

Tasks:


Run ping -c 4 against your default gateway and against a known public host (e.g., 8.8.8.8); compare latency and note any packet loss.
Run traceroute or mtr against the same public host and count the number of hops.
Run ss -tulnp on your lab machine and list every listening service you find, noting which are bound to loopback versus a broader interface.
Use curl -I against a public website and identify the HTTP status code and server header in the response.
Use curl -v against the same site and identify where in the output the TLS handshake occurs.
Run nmap against your own lab machine (localhost or its own IP — always self-authorized) and list every open port found; cross-reference against your ss -tulnp output from Task 3 and confirm they match.
Start a tcpdump capture filtered to port 80 or 443 on your primary interface, then in a separate terminal make a curl request to a website using that protocol, and confirm you can see the resulting traffic in the capture.
Save a short tcpdump capture to a .pcap file and note its file size and packet count.


Deliverable: A short log of commands/output for each task, plus a 2–3 sentence comparison of what ss -tulnp and nmap each told you about your own machine, and why cross-checking both is more reliable than trusting either alone.


Navigation

➡️ [Next: Lesson 04 – DNS](../../docs/Module-03-Linux-Networking/04-dns.md)
