# 📖 Lesson 04 – DNS

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain how DNS resolves hostnames to IP addresses, step by step
- Query DNS records using `dig` and `nslookup`
- Identify common DNS record types and their purposes
- Explain how local name resolution configuration affects a Linux host
- Recognize common DNS-based attack techniques

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 01: Linux Networking Basics
- Lesson 03: Network Utilities (familiarity with `curl`, `ping` helpful for testing resolution)

## Key Terms
| Term | Definition |
|---|---|
| **DNS** | Domain Name System — translates human-readable hostnames into IP addresses |
| **Resolver** | The component that performs DNS lookups on behalf of a client |
| **Record type** | The category of DNS data returned (A, AAAA, MX, TXT, NS, CNAME, etc.) |
| **TTL** | Time To Live — how long a DNS answer may be cached before it must be re-queried |
| **Authoritative server** | The DNS server that holds the actual, definitive records for a domain |
| **DNS cache poisoning** | Injecting false DNS records into a resolver's cache to redirect traffic |
| **DNS exfiltration** | Using DNS queries as a covert channel to smuggle data out of a network |

## Introduction
Every time you type a hostname instead of an IP address, DNS is what makes that work. It's one of the internet's most fundamental pieces of infrastructure — and, because so much depends on it working correctly and being trusted implicitly, it's also a recurring target for attack: from redirecting users to malicious sites, to smuggling stolen data past network defenses inside seemingly innocent-looking queries.

## Why This Matters
DNS sits directly in the path of nearly everything a Linux host does on a network, and both defenders and attackers know it. Defensively, DNS logs are a valuable source of visibility (what domains is this host actually trying to reach?). Offensively, DNS is abused for command-and-control communication, data exfiltration, and redirecting victims to malicious infrastructure — precisely because DNS traffic is so routine that it's often under-monitored compared to more obviously suspicious traffic.

## Cybersecurity Insight
**DNS exfiltration** encodes stolen data into subdomain labels of DNS queries (e.g., `d4t4chunk1.attacker-domain.com`) sent to a domain the attacker controls — because outbound DNS queries are rarely blocked by firewalls (blocking DNS entirely would break normal internet use), this technique can smuggle data out of otherwise well-defended networks. Detecting it typically requires DNS-specific monitoring: unusually long subdomains, high query volume to a single unfamiliar domain, or entropy analysis on subdomain labels — checks that go well beyond a basic firewall configuration.

## Researcher's Note
Threat intelligence researchers frequently track malicious infrastructure through DNS patterns — newly registered domains, domains with unusually short TTLs (associated with "fast flux" techniques that rapidly rotate IP addresses to evade blocklists), or domains resolving to known-malicious hosting are all standard indicators used in DNS-based threat detection. `dig` is one of the primary tools researchers use to directly query and compare DNS records against expected/known-good baselines.

## Real-World Example
DNS cache poisoning attacks, where an attacker tricks a resolver into caching a false IP address for a legitimate domain, have historically been used to redirect victims toward attacker-controlled servers, sometimes for phishing or malware distribution, without the victim ever noticing anything different in their browser address bar. This class of attack is a major reason DNSSEC (DNS Security Extensions, which cryptographically signs DNS records) was developed — though DNSSEC adoption remains inconsistent across the internet, which is itself an important and frequently discussed reality in the field.

---

## Main Topic 1: How DNS Resolution Works

When a Linux host needs to resolve `example.com`, the general flow is:

1. Check the local resolver cache (and `/etc/hosts` — a static, manually maintained override file checked before any network query).
2. Query a configured **recursive resolver** (often your ISP's, a corporate resolver, or a public one like `1.1.1.1`/`8.8.8.8`), listed in `/etc/resolv.conf`.
3. The recursive resolver, if it doesn't already have a cached answer, queries the DNS hierarchy: a **root server**, then a **TLD server** (e.g., for `.com`), then finally the domain's **authoritative server**, which holds the actual definitive record.
4. The answer is returned, cached according to its **TTL**, and given back to the original requester.

```bash
cat /etc/resolv.conf          # see which resolver(s) this host is configured to use
cat /etc/hosts                  # static hostname-to-IP overrides checked before DNS
```
An entry in `/etc/hosts` always takes precedence over DNS for that hostname — a useful fact for testing (temporarily pointing a hostname at a different IP without touching real DNS) and a fact worth checking when a host resolves a name unexpectedly.

## Main Topic 2: Querying DNS — dig and nslookup

**`dig`** is the modern, detailed tool of choice:
```bash
dig example.com                     # basic A record lookup
dig example.com MX                     # mail exchange records
dig example.com TXT                       # text records (often SPF, verification, etc.)
dig example.com NS                           # nameservers for the domain
dig +short example.com                          # just the answer, no extra output
dig @8.8.8.8 example.com                           # query a specific resolver directly
dig -x 8.8.8.8                                        # reverse lookup: IP to hostname
```

**`nslookup`** is older but still common, especially cross-platform:
```bash
nslookup example.com
nslookup example.com 8.8.8.8      # query a specific server
```

Common record types you'll encounter:
| Type | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address |
| AAAA | Maps a hostname to an IPv6 address |
| CNAME | An alias pointing to another hostname |
| MX | Mail server(s) responsible for the domain |
| TXT | Arbitrary text data — commonly used for domain verification, SPF/DKIM email authentication |
| NS | The authoritative nameservers for the domain |
| PTR | Used for reverse lookups (IP to hostname) |

## Main Topic 3: Local Resolution Configuration and Security Considerations

`/etc/resolv.conf` typically looks like:
```
nameserver 8.8.8.8
nameserver 1.1.1.1
```
On many modern distributions, this file is managed automatically by `systemd-resolved` or NetworkManager rather than edited directly — manual edits can be silently overwritten. Checking the actual effective resolver in use:
```bash
resolvectl status          # systemd-resolved based systems
systemd-resolve --status     # older syntax, same idea
```

**Security-relevant DNS considerations:**
- **DNS over HTTPS/TLS (DoH/DoT)** encrypts DNS queries in transit, preventing eavesdropping or tampering by anything sitting on the network path — increasingly common but not universal.
- **DNSSEC** cryptographically signs DNS records so a resolver can verify a response genuinely came from the authoritative source and wasn't tampered with in transit — addressing cache poisoning specifically, though adoption is inconsistent.
- Monitoring outbound DNS query logs for unusual patterns (excessive volume, unusual domains, oddly long subdomain labels) is a practical, high-value detection technique for both malware command-and-control and exfiltration activity, precisely because DNS is so rarely blocked outright.

---

## Tips
- `dig +short` is the fastest way to get a clean answer when you just need the IP, without wading through the full verbose output.
- Always check `/etc/hosts` first when a hostname resolves unexpectedly — it silently overrides DNS and is a common source of "why is this resolving wrong" confusion.
- Use `dig @resolver` to directly compare what different resolvers return for the same hostname — useful for diagnosing propagation delays or suspected cache poisoning/tampering.

## Common Mistakes
- Editing `/etc/resolv.conf` directly on a system where it's managed automatically, then being confused when changes disappear after a reboot or network event.
- Assuming a resolved IP address is automatically trustworthy without considering that DNS responses can be spoofed or tampered with on an untrusted network.
- Forgetting that TTL-based caching means a DNS change (e.g., pointing a domain at a new server) won't be visible everywhere immediately — propagation can take anywhere from minutes to the full TTL duration.
- Overlooking `/etc/hosts` as the explanation for unexpected resolution behavior during troubleshooting.

## Common Misconceptions
- **"DNS just returns IP addresses."** DNS returns many record types beyond A/AAAA — MX, TXT, NS, and others carry critical information for email security, domain verification, and infrastructure delegation.
- **"If HTTPS is used, DNS doesn't matter for security."** Even with HTTPS protecting the content of a connection, an attacker who can manipulate DNS can still redirect a victim to a malicious server (which would need its own valid certificate to fully impersonate the target, but partial attacks and user confusion are still very real risks) — DNS integrity is a meaningful part of overall connection security, not a solved problem just because TLS exists.
- **"Blocking known-malicious domains at the firewall is sufficient DNS defense."** Blocklists are inherently reactive and incomplete; DNS-specific monitoring (volume, entropy, TTL anomalies) catches patterns that static blocklists miss entirely, especially for newly registered or fast-flux domains.

## Quick Practice
```bash
dig +short example.com
cat /etc/resolv.conf
cat /etc/hosts
```
Confirm which resolver your queries are actually using, and check whether `/etc/hosts` contains any entries beyond the default loopback lines.

## Knowledge Check
1. What's the difference between a recursive resolver and an authoritative server?
2. Why does `/etc/hosts` take precedence over DNS queries for a matching hostname?
3. What is DNS exfiltration, and why is it hard to block with a simple firewall rule?
4. What problem does DNSSEC specifically address?
5. What command would you use to query a specific DNS resolver directly, rather than your host's default?

<details>
<summary>Answers</summary>

1. A recursive resolver performs the lookup process on behalf of a client, potentially querying multiple servers along the way; the authoritative server is the definitive source holding the actual records for a specific domain.
2. `/etc/hosts` is checked before any network DNS query as part of standard hostname resolution order, functioning as a static local override.
3. DNS exfiltration encodes stolen data into DNS query subdomains sent to an attacker-controlled domain; it's hard to block because outbound DNS is essential for normal internet use and is rarely blocked wholesale by firewalls.
4. DNSSEC cryptographically signs DNS records so a resolver can verify responses genuinely came from the authoritative source and weren't tampered with, directly addressing cache poisoning/spoofing.
5. `dig @resolver_ip hostname` (e.g., `dig @8.8.8.8 example.com`).
</details>

## Best Practices
- Use `dig` for detailed, scriptable DNS troubleshooting; reserve `nslookup` for quick interactive checks or cross-platform consistency.
- Don't manually edit `/etc/resolv.conf` on systems where it's managed by `systemd-resolved`/NetworkManager — use the proper configuration mechanism instead.
- Monitor DNS query logs for volume and pattern anomalies as part of a broader detection strategy, not just IP/domain blocklists.
- Where feasible, use resolvers and infrastructure that support DNSSEC validation and DoH/DoT for improved integrity and confidentiality of DNS traffic.
- Treat unexpected `/etc/hosts` entries as a potential red flag during a security investigation, since they can be used to silently redirect a specific hostname.

## Summary
DNS translates hostnames into IP addresses through a hierarchical query process, and understanding that process — recursive resolvers, record types, TTLs, and local override mechanisms like `/etc/hosts` — is essential both for everyday troubleshooting and for recognizing DNS-based attack techniques like cache poisoning and exfiltration, which exploit the very trust and ubiquity that make DNS work in the first place.

## What's Next?
With addressing, remote access, diagnostics, and name resolution covered, the final lesson brings it together with firewalls — the mechanism that actually controls what traffic is allowed in and out of a Linux host in the first place.

---

## Navigation
⬅️ [Previous: Lesson 03 – Network Utilities](03-network-utilities.md) | [ Lab 04 : Dns](../../labs/module-03/04-dns.md) | ➡️ [Next: Lesson 05 – Firewall Basics](05-firewall-basics.md)
