🧪 Hands-On Lab: DNS

Objective: Practice DNS querying, record interpretation, and local resolution behavior.

Tasks:


Run dig example.com and identify the answer section, the TTL value, and which resolver answered (from the SERVER: line at the bottom of the output).
Run dig example.com MX and dig example.com TXT and record what each returns; note if any TXT records look like SPF/domain verification entries.
Compare results by querying two different public resolvers directly for the same hostname: dig @8.8.8.8 example.com and dig @1.1.1.1 example.com. Confirm whether the answers match.
Check your host's actual effective resolver configuration with resolvectl status (or inspect /etc/resolv.conf if that command isn't available), and record which resolver(s) are configured.
Add a temporary entry to /etc/hosts mapping a fake hostname (e.g., labtest.local) to 127.0.0.1, then ping labtest.local and confirm it resolves to loopback instead of triggering a real DNS query. Remove the entry when done.
Perform a reverse lookup on a known public IP (e.g., dig -x 8.8.8.8) and record the hostname returned.
Using dig +short, resolve the same hostname three times a few seconds apart and note whether the answer or apparent response time changes, relating your observation to TTL-based caching.


Deliverable: A short log of your commands and their output for each task, plus 2–3 sentences explaining why DNS exfiltration is difficult to block using only a standard firewall rule, connecting it back to what you observed about DNS's ubiquity in this lab.


Navigation

➡️ [Next: Lesson 05 – Firewall Basics](../../docs/Module-03-Linux-Networking/05-firewall-basics.md)
