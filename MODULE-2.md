# 🛡️ Ethical Hacking Course - Module 2
## Networking Fundamentals

---

## 2.1 The OSI Model

### 7 Layers Explained

| Layer | Number | Name | Protocols | Examples |
|-------|--------|------|----------|----------|
| 7 | Application | HTTP, DNS | FTP, SMTP | Web browsing |
| 6 | Presentation | SSL/TLS | JPEG, PNG | Data formatting |
| 5 | Session | RPC, NetBIOS | PPTP | Session management |
| 4 | Transport | TCP, UDP | Port numbers | Data transport |
| 3 | Network | IP, ICMP | Routers | Packet routing |
| 2 | Data Link | Ethernet | MAC addresses | Local networks |
| 1 | Physical | Cables | Fiber, copper | Physical传输 |

### Remember: "Please Do Not Throw Sausage Pizza Away"

---

## 2.2 TCP/IP Protocol Suite

### Key Protocols

```
Application Layer:
├── HTTP/HTTPS (Port 80/443)
├── FTP (Port 20/21)
├── SSH (Port 22)
├── DNS (Port 53)
├── SMTP (Port 25)
└── DHCP (Port 67/68)

Transport Layer:
├── TCP (Reliable, connection-oriented)
└── UDP (Fast, connectionless)

Internet Layer:
├── IP (Addressing)
├── ICMP (Ping, traceroute)
├── ARP (Address resolution)
└── IGMP (Multicast)
```

---

## 2.3 IP Addressing

### IPv4 Structure
```
32-bit address: 192.168.1.100
                └─┬─┘└┬┘└┬┘└──┘
              Octet1 Octet2 Octet3 Octet4

Classes:
Class A: 1.0.0.0 - 126.255.255.255 (/8)
Class B: 128.0.0.0 - 191.255.255.255 (/16)
Class C: 192.0.0.0 - 223.255.255.255 (/24)
Class D: 224.0.0.0 - 239.255.255.255 (Multicast)
Class E: 240.0.0.0 - 255.255.255.255 (Reserved)
```

### Private IP Ranges
```
10.0.0.0 - 10.255.255.255 (10.0.0.0/8)
172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
192.168.0.0 - 192.168.255.255 (192.168.0.0/16)
```

---

## 2.4 Subnetting

### Subnet Mask Basics

| CIDR | Subnet Mask | Hosts |
|------|-------------|-------|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /28 | 255.255.255.240 | 14 |
| /30 | 255.255.255.252 | 2 |

### Example: /24 Network
```
Network: 192.168.1.0/24
First IP: 192.168.1.1
Last IP: 192.168.1.254
Broadcast: 192.168.1.255
```

---

## 2.5 Common Ports

### Essential Ports to Know

| Port | Service | Protocol |
|------|---------|----------|
| 21 | FTP | TCP |
| 22 | SSH | TCP |
| 23 | Telnet | TCP |
| 25 | SMTP | TCP |
| 53 | DNS | TCP/UDP |
| 80 | HTTP | TCP |
| 110 | POP3 | TCP |
| 143 | IMAP | TCP |
| 443 | HTTPS | TCP |
| 445 | SMB | TCP |
| 3306 | MySQL | TCP |
| 3389 | RDP | TCP |
| 5432 | PostgreSQL | TCP |

---

## 2.6 Network Devices

### Devices by Layer

```
Layer 1: Hub, Repeater
Layer 2: Switch, Bridge
Layer 3: Router
Layer 4+: Load Balancer, Firewall

Switch vs Hub:
Hub: Broadcasts to all ports (security risk)
Switch: Sends to specific MAC address (secure)
```

---

## 2.7 Firewalls & IDS/IPS

### Firewall Types

| Type | Function |
|------|----------|
| Packet Filter | Checks packet headers |
| Stateful Inspection | Tracks connections |
| Proxy | Intermediate server |
| Next-Gen | Deep packet inspection |

### IDS vs IPS

```
IDS (Intrusion Detection System):
- Monitors traffic
- Alerts on threats
- Passive (out of band)

IPS (Intrusion Prevention System):
- Active protection
- Blocks threats
- Inline (in path)
```

---

## 2.8 Network Reconnaissance

### Commands to Know

```bash
# Check IP configuration
ipconfig (Windows)
ip addr (Linux)

# Test connectivity
ping <target>

# Trace route
traceroute <target> (Linux)
tracert <target> (Windows)

# Lookup DNS
nslookup <domain>
dig <domain>

# View network connections
netstat -an
ss -tulpn
```

---

## Key Takeaways

✅ OSI model is fundamental
✅ TCP/IP is the internet protocol
✅ Subnetting enables network segmentation
✅ Firewalls protect networks
✅ Know your ports!

---

*Module 2 Complete! Next: Linux for Hackers 🛡️*
