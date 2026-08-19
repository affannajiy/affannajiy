# Networking Notes

> My running study log for computer networking. Notes to my future self — keep it practical.

_Last updated: 18/7/2026_

---

## 📖 Core Concepts

### The OSI & TCP/IP Models
| OSI Layer | TCP/IP | Example |
|---|---|---|
| 7. Application | Application | HTTP, DNS, SSH |
| 6. Presentation | Application | TLS, encoding |
| 5. Session | Application | — |
| 4. Transport | Transport | TCP, UDP |
| 3. Network | Internet | IP, ICMP |
| 2. Data Link | Link | Ethernet, ARP, MAC |
| 1. Physical | Link | cables, Wi-Fi radio |

> _Fill in your own examples/mnemonics as you learn each layer._

### IP Addressing & Subnetting
- IPv4 = 32 bits, written as 4 octets (e.g. `192.168.1.10`).
- CIDR `/24` = first 24 bits are the network → 256 addresses (254 usable).
- Private ranges: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

_(TODO: subnetting practice — how to split a /24 into /26s)_

### TCP vs UDP
| | TCP | UDP |
|---|---|---|
| Connection | Yes (handshake) | No |
| Reliable | Yes (retransmits) | No |
| Ordered | Yes | No |
| Use case | Web, files, SSH | Streaming, DNS, games |

---

## 🧪 Labs & Practice
- _(log hands-on stuff here: Wireshark captures, ping/traceroute experiments, Cisco Packet Tracer, etc.)_

## 🔧 Handy Commands
```bash
ping <host>            # basic reachability
traceroute <host>      # path to host (tracert on Windows)
ipconfig / ip addr     # your own addresses
nslookup <domain>      # DNS lookup
netstat -an            # active connections/ports
```

## ❓ Questions to Revisit
- _(things that confused you — come back and answer them later)_

## 🔗 Resources
- _(add courses, videos, docs you found useful)_
