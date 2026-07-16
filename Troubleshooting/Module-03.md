# 🛠️ Module Troubleshooting Guide

A single reference for diagnosing common problems across every topic in this module. Organized by lesson so you can jump straight to the relevant section.

---

## Linux Networking Basics

**Host has an IP address but can't reach anything outside its local subnet**
- Cause: missing or incorrect default gateway.
- Fix: check `ip route show` for a `default via ...` line; add one if missing (`sudo ip route add default via <gateway_ip>`), and check the interface's DHCP configuration if it should be assigned automatically.

**A service you configured seems unreachable from other machines**
- Cause: it's likely bound to `127.0.0.1` (loopback) instead of the host's real interface, or a firewall is blocking it (see the Firewall section below).
- Fix: check the service's own configuration for its bind address, and confirm with `ss -tulnp` (Lesson 03) which address it's actually listening on.

**Two hosts on what looks like the same network can't reach each other**
- Cause: they may actually be on different subnets despite looking similar (e.g., different CIDR prefixes), or one has an incorrect subnet mask.
- Fix: compare `ip addr show` output from both hosts carefully, including the CIDR prefix, not just the IP address itself.

---

## SSH

**"Permission denied (publickey)" when connecting**
- Cause: wrong key specified, key not deployed to the server, or incorrect permissions on `~/.ssh` or `authorized_keys`.
- Fix: use `ssh -v` to see which keys were offered and why each was rejected; verify `~/.ssh` is `700` and `authorized_keys` is `600` on the server; confirm the correct public key is actually present in `authorized_keys`.

**Locked out after restarting sshd with a bad config**
- Cause: skipped `sshd -t` validation before restarting, or a syntax/logic error in `sshd_config` that broke authentication entirely.
- Fix: this requires out-of-band access (console, cloud provider serial console, physical access) to fix — which is exactly why the lesson emphasizes testing config and keeping a session open before restarting. Prevention is the real fix here.

**Connection hangs indefinitely with no error**
- Cause: often a firewall (local or network) silently dropping the connection rather than actively refusing it, or a routing issue.
- Fix: try `ssh -v` to see how far the connection gets; check firewall rules on both ends (Lesson 05); confirm basic reachability with `ping`/`traceroute` first (Lesson 03).

**Password authentication stopped working after a hardening change, but you needed it**
- Cause: `PasswordAuthentication no` was set in `sshd_config`.
- Fix: this is expected behavior after that hardening step; if password auth is genuinely still needed, set it back to `yes` deliberately and understand the tradeoff, or switch fully to key-based auth instead.

---

## Network Utilities

**`ping` fails but the service is actually reachable via other protocols**
- Cause: ICMP is commonly blocked by firewalls even when other traffic is allowed — this is normal, expected behavior on many hardened hosts.
- Fix: don't rely on `ping` alone to determine reachability; test the actual protocol/port in question with `curl`, `nc`, or a targeted `nmap` scan.

**`nmap` shows a port as `filtered` instead of `open` or `closed`**
- Cause: no response was received at all, consistent with a firewall silently dropping probes rather than actively rejecting them.
- Fix: this is often expected/intended firewall behavior — confirm against the host's actual firewall configuration rather than assuming a misconfiguration.

**`tcpdump` shows "permission denied"**
- Cause: packet capture requires elevated privileges.
- Fix: run with `sudo`.

**`ss`/`netstat` shows a process as unknown or blank**
- Cause: insufficient privileges to see process ownership details for sockets owned by other users.
- Fix: re-run with `sudo` to see full process attribution.

---

## DNS

**A hostname resolves to the wrong IP address**
- Cause: often an `/etc/hosts` entry silently overriding DNS, or a stale cached answer still within its TTL.
- Fix: check `/etc/hosts` first; if not present there, try querying a different resolver directly with `dig @resolver hostname` to rule out local cache/resolver-specific issues.

**Changes to `/etc/resolv.conf` don't persist**
- Cause: the file is being managed automatically by `systemd-resolved` or NetworkManager and gets regenerated.
- Fix: use `resolvectl` or the network manager's proper configuration mechanism instead of editing the file directly.

**DNS queries are slow or timing out**
- Cause: could be the configured resolver being slow/unreachable, or a network path issue.
- Fix: test directly against a known-fast public resolver (`dig @1.1.1.1 hostname`) to isolate whether the problem is the resolver or the broader network path.

**A domain seems to resolve differently from different networks/locations**
- Cause: could be legitimate (CDN/load-balancing behavior returning different answers by design) or could indicate cache poisoning/tampering on one of the networks.
- Fix: compare answers from multiple independent, trusted resolvers; investigate further if answers point to unexpected or suspicious infrastructure.

---

## Firewall Basics

**Locked out of SSH immediately after enabling the firewall**
- Cause: enabled a default-deny policy without first allowing SSH.
- Fix: requires out-of-band access (console/physical) to add the missing allow rule — this is precisely the scenario the lesson's warnings are designed to prevent. Always allow SSH before enabling a default-deny policy.

**Firewall rules disappear after a reboot**
- Cause: rules were added directly via `iptables`/`nftables` command line without a persistence step, or `firewall-cmd` changes were made without `--permanent`.
- Fix: use `iptables-save`/your distribution's persistence mechanism, or always include `--permanent` with `firewall-cmd` followed by `--reload`.

**A rule was added but doesn't seem to have any effect**
- Cause: likely a rule ordering issue — an earlier, broader rule in the chain is matching the traffic first (iptables evaluates top-down).
- Fix: review the full rule list in order (`iptables -L -n -v --line-numbers`) and confirm your new rule isn't shadowed by an earlier one; reposition if needed.

**`ufw enable` reports success but a service still seems unreachable**
- Cause: either no allow rule exists for that specific port/protocol, or the service itself isn't actually listening/bound correctly (a networking, not firewall, issue).
- Fix: confirm the service is listening at all first with `ss -tulnp` (Lesson 03) before assuming it's a firewall problem; then confirm the specific port/protocol has an explicit allow rule.

---

## General Diagnostic Workflow

When something doesn't behave as expected anywhere in this module, work through these questions in order:

1. **Is there basic reachability at all?** — `ping`, `traceroute`/`mtr` (remembering ICMP may be blocked even when other traffic works)
2. **Is the service actually listening, and where?** — `ss -tulnp`, checking the bind address (loopback vs. broader interface)
3. **Is a firewall involved?** — check both local (`ufw`/`firewalld`/`iptables`) and any network-level firewall between the two hosts
4. **Is name resolution correct?** — `dig`, check `/etc/hosts`, compare against a known-good resolver
5. **What does the actual traffic look like?** — `tcpdump` for ground truth when higher-level tools disagree or seem wrong
6. **Do I have an escape hatch?** — before changing SSH or firewall configuration remotely, always confirm an alternate access path exists first
