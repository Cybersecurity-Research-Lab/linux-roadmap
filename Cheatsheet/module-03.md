# 📑 Module Cheat Sheet

One-page quick reference for every command covered in this module. Keep this open in a second window while you work.

---

## 🌐 Linux Networking Basics

```bash
# Interfaces and addressing
ip addr show              # all interfaces + addresses
ip a                         # shorthand
ip addr show eth0              # specific interface
ifconfig                          # older tool (net-tools)

# Routing
ip route show               # routing table
ip r                           # shorthand
route -n                         # older tool, numeric

# CIDR quick reference
/24 = 256 addrs (254 usable)   /16 = 65,536 addrs   /8 = 16,777,216 addrs

# Common well-known ports
22=SSH   53=DNS   80=HTTP   443=HTTPS   3389=RDP
```

---

## 🔑 SSH

```bash
# Connecting
ssh user@host
ssh -p 2222 user@host
ssh -i ~/.ssh/mykey user@host
ssh user@host "command"          # run one command, exit
ssh -v user@host                    # verbose (troubleshooting)

# File transfer
scp file user@host:/path/
scp -r dir/ user@host:/path/
rsync -avz dir/ user@host:/path/    # preferred for repeated/large transfers

# Key management
ssh-keygen -t ed25519 -C "you@example.com"
ssh-copy-id user@host
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519          # private key
chmod 644 ~/.ssh/id_ed25519.pub       # public key

# ~/.ssh/config shortcut
Host myserver
    HostName 203.0.113.10
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_ed25519

# Server hardening (/etc/ssh/sshd_config)
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
AllowUsers alice bob

sudo sshd -t                    # ALWAYS validate before restarting
sudo systemctl restart sshd        # keep an existing session open when testing!

# fail2ban
sudo systemctl enable --now fail2ban
fail2ban-client status sshd
```

---

## 🧰 Network Utilities

```bash
# Reachability
ping -c 4 host
traceroute host
mtr host                       # live ping + traceroute

# Listening ports / connections
ss -tulnp          # TCP+UDP, listening, numeric, with process — THE go-to command
ss -tan               # all TCP connections
netstat -tulnp           # older equivalent

# HTTP requests
curl -I https://example.com          # headers only
curl -v https://example.com             # verbose, full handshake
curl -X POST -d '{"k":"v"}' -H "Content-Type: application/json" URL
wget https://example.com/file

# Port scanning (AUTHORIZED TARGETS ONLY)
nmap host
nmap -p 1-1000 host
nmap -p- host              # all 65535 ports
nmap -sV host                 # service version detection
nmap -A host                     # aggressive: OS/version/scripts/traceroute

# Packet capture
sudo tcpdump -i eth0
sudo tcpdump -i eth0 port 443
sudo tcpdump -i eth0 host 192.168.1.10
sudo tcpdump -i eth0 -w capture.pcap    # save for Wireshark analysis
```

---

## 🔍 DNS

```bash
# Querying
dig example.com                  # A record
dig example.com MX                  # mail records
dig example.com TXT                    # text records (SPF, verification)
dig example.com NS                        # nameservers
dig +short example.com                       # answer only, no extra output
dig @8.8.8.8 example.com                        # query a specific resolver
dig -x 8.8.8.8                                     # reverse lookup

nslookup example.com
nslookup example.com 8.8.8.8       # query a specific server

# Local resolution
cat /etc/resolv.conf         # configured resolver(s)
cat /etc/hosts                  # static overrides (checked BEFORE DNS)
resolvectl status                  # effective resolver (systemd-resolved systems)

# Record type quick reference
A=IPv4   AAAA=IPv6   CNAME=alias   MX=mail   TXT=text/SPF/DKIM   NS=nameservers   PTR=reverse lookup
```

---

## 🧱 Firewall Basics

```bash
# --- ufw (Debian/Ubuntu) ---
sudo ufw status verbose
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp                  # ALWAYS allow SSH before enabling!
sudo ufw allow 80/tcp
sudo ufw allow from 192.168.1.0/24 to any port 5432
sudo ufw deny 23
sudo ufw enable
sudo ufw delete allow 80/tcp

# --- firewalld (RHEL/Fedora) ---
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --zone=public --list-all
sudo firewall-cmd --zone=public --add-service=ssh --permanent
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload                 # required to apply --permanent changes
sudo firewall-cmd --zone=public --remove-service=ssh --permanent

# --- iptables (legacy, still very common) ---
sudo iptables -L -n -v
sudo iptables -P INPUT DROP                  # default-deny inbound
sudo iptables -P FORWARD DROP
sudo iptables -A INPUT -i lo -j ACCEPT          # always allow loopback
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables-save > /etc/iptables/rules.v4      # persist (path varies by distro)

# --- nftables (modern) ---
sudo nft list ruleset
sudo nft add rule inet filter input tcp dport 22 accept
```

---

## 🚨 Quick Security Audit Checklist

```bash
ss -tulnp                                   # what's actually listening, and where
ip addr show                                   # confirm interface bindings (loopback vs. broad)
sudo sshd -t && cat /etc/ssh/sshd_config | grep -E "PermitRootLogin|PasswordAuthentication"
sudo ufw status verbose   /   sudo firewall-cmd --list-all   /   sudo iptables -L -n -v
dig +short yourdomain.com                          # confirm expected DNS answers
cat /etc/hosts                                        # check for unexpected static overrides
sudo tcpdump -i eth0 -c 50                               # quick live traffic sanity check
nmap -sV localhost                                          # cross-check against ss -tulnp
```

---

## ⚠️ Universal Safety Reminders

- **Before any SSH or firewall change on a remote system**: confirm an out-of-band access path (console, physical access) exists.
- **Before enabling a default-deny firewall**: explicitly allow SSH first.
- **Before restarting `sshd`**: run `sshd -t` and keep your current session open.
- **Before running `nmap` against any target**: confirm you have explicit authorization.
