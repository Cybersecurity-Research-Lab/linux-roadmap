# 📖 Lesson 01 – Linux Networking Basics

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain IP addressing, subnetting basics, and the role of the default gateway
- Inspect network interfaces and their configuration on a Linux host
- Explain the difference between TCP and UDP, and the concept of ports
- Read and interpret basic routing table entries
- Recognize common network misconfigurations relevant to security

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟢 Beginner

## Prerequisites
- Comfortable typing commands in a Linux terminal
- No prior networking experience required, though basic familiarity helps

## Key Terms
| Term | Definition |
|---|---|
| **IP address** | A numeric address identifying a device on a network (IPv4 or IPv6) |
| **Subnet mask / CIDR** | Defines which portion of an IP address identifies the network vs. the host |
| **Interface** | A network endpoint on a host (physical NIC, virtual, or loopback) |
| **Gateway** | The router a host sends traffic to when the destination isn't on its local network |
| **Port** | A numbered endpoint (0–65535) that lets multiple services share one IP address |
| **TCP** | Transmission Control Protocol — connection-oriented, reliable, ordered delivery |
| **UDP** | User Datagram Protocol — connectionless, faster, no delivery guarantees |
| **Routing table** | The kernel's list of rules for which interface/gateway to use for a given destination |

## Introduction
Before you can secure, monitor, or troubleshoot a network, you need a working mental model of how Linux sees the network around it: interfaces, addresses, routes, and ports. This lesson builds that foundation — not exhaustive networking theory, but the practical subset every Linux administrator and security practitioner uses daily.

## Why This Matters
Nearly every security control and every attack technique touches the network layer in some way: firewalls filter based on IP/port, lateral movement depends on routing and reachability, and misconfigured interfaces or overly permissive routes can expose services that were never meant to be internet-facing. You can't reason about network security without first being able to read a host's actual network configuration.

## Cybersecurity Insight
A service accidentally bound to `0.0.0.0` (all interfaces) instead of `127.0.0.1` (localhost only) is one of the most common real-world exposure mistakes — it turns an internal-only tool (a database, an admin panel, a debug interface) into something reachable from the public internet if the host has a public IP and no firewall blocking it. Checking what's actually listening and on which interface (covered in Lesson 03) is a direct extension of understanding interfaces and addressing from this lesson.

## Researcher's Note
Network reconnaissance during a penetration test typically starts by mapping which hosts exist and which ports are open on each — but interpreting those results correctly requires understanding the addressing and routing concepts in this lesson first. A researcher who doesn't understand the difference between a host being unreachable due to routing versus a host actively refusing a connection (firewall/closed port) will draw the wrong conclusions from their scan results.

## Real-World Example
A well-documented category of cloud misconfiguration involves administrators binding a database (like Redis or MongoDB) to all interfaces during initial setup "to get it working," intending to restrict it later with a firewall rule — and then never following through. Internet-wide scanning research has repeatedly found large numbers of such databases exposed with no authentication, directly traceable to this interface-binding mistake combined with a missing firewall rule.

---

## IP Addressing and Interfaces

Every network-connected Linux host has one or more **interfaces** — physical (like `eth0`, `enp0s3`) or virtual (like `lo`, the loopback interface, or `docker0`). Each interface can have one or more IP addresses assigned.

```bash
ip addr show          # modern tool, shows all interfaces and addresses
ip a                    # shorthand
ip addr show eth0          # a specific interface
ifconfig                     # older tool, may need installing (net-tools package)
```
Example output interpretation:
```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0
```
`192.168.1.50/24` means the host's address is `192.168.1.50`, and `/24` (CIDR notation) means the first 24 bits identify the network — equivalent to a subnet mask of `255.255.255.0`, giving a network of 254 usable host addresses (`192.168.1.1`–`192.168.1.254`).

The **loopback interface** (`lo`, typically `127.0.0.1`) lets a host talk to itself — many services (databases, local APIs) are deliberately bound only to loopback so they're unreachable from any other machine, which is an important security design pattern referenced throughout this module.

##  Routing and Gateways

When a host wants to reach an address outside its local subnet, it consults its **routing table** to decide which interface and gateway to use:
```bash
ip route show          # display the routing table
ip r                      # shorthand
route -n                    # older tool, numeric output
```
Typical output:
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.50
```
The `default via 192.168.1.1` line is the **default gateway** — where traffic goes when no more specific route matches. The second line says "anything in the `192.168.1.0/24` network is reachable directly, no gateway needed."

Tracing where traffic actually goes hop by hop is covered with `traceroute`/`mtr` in Lesson 03, but understanding the routing table conceptually here is what makes that tool's output meaningful rather than mysterious.

##  TCP, UDP, and Ports

A single IP address can host many simultaneous network services, distinguished by **port number** (0–65535). A few reserved, well-known ports you'll see constantly:

| Port | Service |
|---|---|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

**TCP** is connection-oriented: before data flows, a three-way handshake (`SYN` → `SYN-ACK` → `ACK`) establishes a reliable, ordered connection — used for HTTP(S), SSH, and most services where data integrity and completeness matter.

**UDP** is connectionless: packets ("datagrams") are sent without establishing a session or guaranteeing delivery or order — used for DNS queries, video streaming, and other cases where speed matters more than guaranteed delivery, and the application layer handles any needed reliability itself.

This distinction matters directly for security tooling: a TCP port scan behaves very differently from a UDP port scan (UDP scanning is slower and less reliable specifically because there's no handshake to confirm a response), and firewall rules are commonly written per-protocol (`tcp`/`udp`) as well as per-port.

---

## Tips
- `ip` is the modern, actively maintained tool; `ifconfig`, `route`, and `netstat` are older but still extremely common in documentation, scripts, and muscle memory — it's worth being comfortable with both families.
- `/24`, `/16`, `/8` are the CIDR notations you'll see constantly; memorizing that `/24` = 256 addresses (254 usable) covers a large fraction of real-world small-network scenarios.
- Loopback-only binding (`127.0.0.1`) is one of the simplest and most effective ways to keep a service unreachable from the network without even touching the firewall.

## Common Mistakes
- Confusing a host's IP address with its subnet — `/24` describes the network size, not a property of the individual address.
- Assuming a service is "internal only" because it's not documented publicly, without verifying it's actually bound to loopback or protected by a firewall.
- Treating `ifconfig` output as authoritative on systems where it's not installed by default (many modern distributions ship only `ip` out of the box).
- Forgetting that a missing or incorrect default gateway will make a host unable to reach anything outside its local subnet, even though its own interface and IP look correctly configured.

## Common Misconceptions
- **"My server isn't reachable because it doesn't have a public IP listed anywhere."** Reachability depends on actual routing and firewall rules, not on whether an address appears in documentation — internet-wide scanners routinely discover and probe every public IPv4 address regardless of whether it's been "announced" anywhere.
- **"TCP is always more secure than UDP."** TCP and UDP differ in reliability/ordering guarantees, not inherent security — either can carry encrypted or unencrypted traffic, and security depends on the application-layer protocol running on top (e.g., a TCP connection can carry plaintext just as easily as UDP can).
- **"A private IP address (like 192.168.x.x) means the service is safe from the internet."** Private addressing only protects against direct internet reachability if there's no NAT/port-forwarding or misconfigured routing exposing it — many real breaches involve internal-only assumptions that turned out to be wrong.

## Quick Practice
```bash
ip addr show
ip route show
```
Identify your host's primary IP address, its CIDR prefix, and its default gateway from the output.

## Knowledge Check
1. What does the `/24` in `192.168.1.50/24` tell you?
2. What is the purpose of the loopback interface, and why is it security-relevant?
3. What's the fundamental difference between TCP and UDP?
4. What routing table entry determines where traffic goes when no more specific route matches?
5. Why might a private IP address still be reachable from the internet?

<details>
<summary>Answers</summary>

1. It indicates the subnet mask — the first 24 bits identify the network portion of the address, leaving 8 bits (254 usable addresses) for hosts.
2. The loopback interface (`127.0.0.1`/`lo`) lets a host communicate with itself; binding a service to loopback only is a common way to keep it unreachable from any other machine on the network.
3. TCP is connection-oriented with a handshake and guarantees reliable, ordered delivery; UDP is connectionless with no delivery or ordering guarantees, favoring speed.
4. The `default` route (default gateway) entry.
5. Because of NAT/port-forwarding rules or routing misconfigurations that expose an internally-addressed service to external traffic despite its private IP.
</details>

## Best Practices
- Bind services to `127.0.0.1` (loopback) by default, and only bind to a broader interface (`0.0.0.0` or a specific public IP) when external access is genuinely required and protected by other controls.
- Use `ip` rather than deprecated tools (`ifconfig`, `route`) for new scripts and documentation, since it's the actively maintained standard.
- Document expected routing and interface configuration for critical hosts so deviations are easy to spot during an audit.
- Understand the TCP/UDP distinction when writing firewall rules — filtering one protocol on a port doesn't automatically filter the other.

## Summary
Linux networking fundamentals — interfaces, IP addressing, routing, and the TCP/UDP/port model — are the foundation every other topic in this module builds on. Being able to read `ip addr` and `ip route` output fluently, and understanding why a service bound to the wrong interface becomes an exposure risk, sets up everything from SSH hardening to firewall configuration later in this module.

## What's Next?
With the addressing and routing foundation in place, Lesson 02 covers SSH — the primary way Linux systems are administered remotely, and a frequent target for both legitimate access and attack.

---


## Navigation
⬅️ [Back to Module README](README.md) | [Lab 01: Linux Networking basics](../../labs/module-03/01-linux-networking-basics.md)  ➡️ [Next: Lesson 02 – SSH](02-ssh.md)
