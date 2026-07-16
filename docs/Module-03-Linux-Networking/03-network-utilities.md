# 📖 Lesson 03 – Network Utilities

## Learning Outcomes
By the end of this lesson, you will be able to:
- Test connectivity and diagnose reachability issues using `ping` and `traceroute`/`mtr`
- Identify listening ports and active connections using `ss`/`netstat`
- Transfer data and interact with web services using `curl`/`wget`
- Perform basic network reconnaissance using `nmap`
- Capture and inspect live network traffic using `tcpdump`

## Estimated Study Time
60 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 01: Linux Networking Basics
- Lesson 02: SSH (useful for accessing lab hosts)

## Key Terms
| Term | Definition |
|---|---|
| **Latency** | The time delay for a packet to travel from source to destination |
| **Listening port** | A port on which a process is actively waiting to accept incoming connections |
| **Packet capture** | Recording raw network traffic for inspection |
| **Port scan** | Systematically probing a range of ports to determine which are open |
| **Three-way handshake** | The TCP connection establishment sequence: SYN, SYN-ACK, ACK |
| **Promiscuous mode** | A network interface mode that captures all traffic on the segment, not just traffic addressed to it |

## Introduction
This lesson is a toolkit lesson: a set of utilities every Linux administrator and security practitioner reaches for constantly. `ping` and `traceroute` answer "can I reach it, and how?" `ss`/`netstat` answer "what's listening, and to whom am I connected?" `curl`/`wget` interact directly with web services. `nmap` maps out what's reachable and open across a network. `tcpdump` shows you the actual traffic, packet by packet. Together, these tools cover the large majority of day-to-day network diagnostics and a meaningful chunk of security reconnaissance work.

## Why This Matters
These utilities are the shared vocabulary of network troubleshooting and network security work alike — the same `nmap` scan a system administrator runs to verify a firewall change is the same fundamental technique a penetration tester uses during reconnaissance. Fluency with this toolkit lets you quickly answer "is this service actually reachable," "what's actually listening on this host," and "what does this traffic actually look like" without guessing.

## Cybersecurity Insight
`ss -tulnp` (or the older `netstat -tulnp`) reveals every listening port on a host along with the process behind it — this single command is one of the fastest ways to audit a system for unexpected exposed services, and it's a standard first step in both routine hardening reviews and incident response triage. Comparing its output against an expected baseline (what *should* be listening) is far more reliable than assuming a service isn't exposed just because you don't remember configuring it that way.

## Researcher's Note
`nmap` is one of the most widely used tools in network security research and penetration testing precisely because it goes beyond simple reachability — it can fingerprint operating systems, identify service versions, and (with scripting engine extensions) probe for known vulnerabilities. Researchers use it both offensively (mapping an authorized target's attack surface) and defensively (scanning their own infrastructure to confirm only intended ports are actually reachable from outside).

## Real-World Example
Internet-wide scanning studies (research projects that continuously `nmap`-style scan the entire public IPv4 address space) have repeatedly found large numbers of hosts exposing services like databases, remote management interfaces, and industrial control protocols directly to the internet with no authentication — findings made possible entirely by the same basic port-scanning technique covered in this lesson, applied at massive scale. This is also why organizations run their own regular external scans against their own infrastructure, specifically to catch this class of exposure before an attacker's scan does.

---

##  Connectivity Testing — ping, traceroute, mtr

```bash
ping host                    # send ICMP echo requests, Ctrl+C to stop
ping -c 4 host                 # send exactly 4 and stop
traceroute host                  # show the hop-by-hop path to a destination
mtr host                            # combines ping + traceroute, live updating
```
`ping` confirms basic reachability and gives a rough latency figure; a full timeout usually means either the host is down, unreachable due to routing, or a firewall is silently dropping ICMP (a very common and often intentional firewall behavior — "no ping response" does not necessarily mean "host is down").

`traceroute`/`mtr` show every router hop between you and the destination, which is invaluable for diagnosing *where* connectivity breaks down on a multi-hop path, rather than just knowing that it did.

##  Connections and Listening Ports — ss and netstat

```bash
ss -tulnp          # TCP+UDP, listening, numeric, show process — the single most useful combo
ss -tan               # all TCP connections, numeric
netstat -tulnp           # older equivalent, may need installing (net-tools)
```
Flag breakdown for `ss -tulnp`: `-t` TCP, `-u` UDP, `-l` listening only, `-n` numeric (skip DNS lookups, faster and avoids leaking queries), `-p` show the owning process.

Example output line:
```
tcp   LISTEN  0  128  127.0.0.1:5432   0.0.0.0:*   users:(("postgres",pid=1234,fd=6))
```
This tells you PostgreSQL is listening on `127.0.0.1:5432` (loopback only — not reachable externally, tying directly back to Lesson 01's interface-binding discussion) with PID 1234.

##  Web Requests, Port Scanning, and Packet Capture

**`curl`/`wget`** — command-line tools for making HTTP(S) requests, essential for testing web services and APIs directly:
```bash
curl -I https://example.com              # headers only, quick reachability/response check
curl -v https://example.com                 # verbose, shows the full request/response and TLS handshake
curl -X POST -d '{"key":"value"}' -H "Content-Type: application/json" https://api.example.com/endpoint
wget https://example.com/file.zip              # download a file
```

**`nmap`** — network/port scanning:
```bash
nmap host                        # default scan, common ports
nmap -p 1-1000 host                 # scan a specific port range
nmap -p- host                          # scan all 65535 ports (slow but thorough)
nmap -sV host                            # detect service versions on open ports
nmap -A host                                # aggressive: OS detection, version detection, scripts, traceroute
```
⚠️ Only scan hosts and networks you own or have explicit written authorization to test — unauthorized scanning of systems you don't control can violate computer misuse laws in many jurisdictions, regardless of intent.

**`tcpdump`** — raw packet capture:
```bash
sudo tcpdump -i eth0                          # capture on a specific interface
sudo tcpdump -i eth0 port 443                    # filter by port
sudo tcpdump -i eth0 host 192.168.1.10              # filter by host
sudo tcpdump -i eth0 -w capture.pcap                   # write to a file for later analysis (e.g., in Wireshark)
```
`tcpdump` shows you what's actually on the wire, which is the ground truth when higher-level tools give confusing or contradictory results — it's often the last resort in a troubleshooting or investigation workflow, and the first resort when analyzing a suspected network-based intrusion.

---

## Tips
- `ss` is the modern replacement for `netstat` and is faster and more actively maintained — prefer it in new scripts and workflows, but recognize `netstat` syntax since it's everywhere in older documentation.
- Add `-n` to almost any of these tools when you want raw IPs/ports instead of resolved hostnames — it's faster and avoids triggering unnecessary DNS lookups during an investigation.
- Save `tcpdump` captures to a `.pcap` file with `-w` and analyze them later in Wireshark for a much easier visual/protocol-aware view than reading raw terminal output.

## Common Mistakes
- Assuming a host with no `ping` response is definitely down, when ICMP may simply be blocked by a firewall.
- Running `nmap` against systems without authorization, even "just to check," which can have serious legal consequences.
- Forgetting `sudo` for `tcpdump` (raw packet capture requires elevated privileges) and getting a permission error instead of understanding why.
- Using `curl` without `-I` or `-v` when troubleshooting, missing the headers/status codes that actually explain a failure.

## Common Misconceptions
- **"A closed port and a filtered (firewalled) port look the same in a scan."** `nmap` distinguishes between `closed` (host responded, nothing listening) and `filtered` (no response at all, consistent with a firewall silently dropping the probe) — the distinction matters a great deal for troubleshooting versus reconnaissance interpretation.
- **"tcpdump requires deep protocol expertise to be useful."** Basic host/port filtering and counting packets is useful even for a beginner; deep protocol analysis is a skill you build over time, but tcpdump earns its keep long before you're an expert.
- **"nmap scanning your own systems has no risk."** Aggressive scans can occasionally disrupt fragile services or trigger alerting/security responses even on your own infrastructure — coordinate scan timing and scope with anyone else responsible for the target systems.

## Quick Practice
```bash
ss -tulnp
curl -I https://example.com
ping -c 4 8.8.8.8
```
Identify at least one listening service on your own machine and confirm whether it's bound to loopback or a broader interface.

## Knowledge Check
1. Why doesn't a failed `ping` necessarily mean a host is down?
2. What does the `-l` flag do in `ss -tulnp`, and why is it commonly combined with `-p`?
3. What's the difference between a `closed` and a `filtered` port in an `nmap` scan result?
4. Why is authorization required before running `nmap` against a target?
5. What's a practical reason to save `tcpdump` output to a `.pcap` file rather than only reading it live in the terminal?

<details>
<summary>Answers</summary>

1. Many hosts/firewalls deliberately block or silently drop ICMP echo requests, so no response doesn't confirm the host is unreachable for other protocols/ports.
2. `-l` restricts output to listening sockets only (services waiting for connections, rather than all connections); combining with `-p` shows which process owns each listening socket, which is essential for identifying what's actually running a given service.
3. `closed` means the host responded but nothing is listening on that port; `filtered` means no response was received at all, consistent with a firewall silently dropping the probe rather than the port simply being unused.
4. Unauthorized scanning of systems you don't own or control can violate computer misuse laws in many jurisdictions, regardless of the scanner's intent.
5. It allows deeper, repeatable analysis later (e.g., in Wireshark) without needing to keep the live capture running, and preserves evidence for incident investigation.
</details>

## Best Practices
- Use `ss -tulnp` regularly on systems you manage to maintain an accurate baseline of what should be listening.
- Always get explicit authorization before scanning any system you don't personally own.
- Prefer `ss` over `netstat` and `ip` over `ifconfig`/`route` in new work, while still recognizing the older tools.
- Save packet captures to disk for anything you might need to review or share later, rather than relying on live terminal output alone.
- Combine multiple tools when troubleshooting — `ping` for basic reachability, `traceroute`/`mtr` for path issues, `ss` for local listening state, `tcpdump` for ground-truth traffic — rather than relying on just one.

## Summary
This toolkit — `ping`/`traceroute`/`mtr` for reachability, `ss`/`netstat` for local connection state, `curl`/`wget` for web interaction, `nmap` for reconnaissance, and `tcpdump` for raw packet truth — covers the large majority of practical network diagnostics and forms a core part of the shared vocabulary between system administration and security work.

## What's Next?
Several of these tools depend on name resolution working correctly behind the scenes. Lesson 04 covers DNS — how hostnames become IP addresses, and how that system is both essential infrastructure and a frequent attack target.

---


## Navigation
⬅️ [Previous: Lesson 02 – SSH](02-ssh.md) | [ Lab 03 : Network Utilities](../../labs/module-03/03-network-utilities.md) | ➡️ [Next: Lesson 04 – DNS](04-dns.md)
