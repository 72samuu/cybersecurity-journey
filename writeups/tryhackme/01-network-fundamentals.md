# Network Fundamentals — TryHackMe module notes

**Platform:** TryHackMe · **Module:** Network Fundamentals · **Badge earned:** Networking Nerd
**Rooms:** What is Networking? · Intro to LAN · OSI Model · Packets & Frames · Extending Your Network

These are my notes from the module, written in my own words. No flags or answers are included.

---

## 1. What is Networking?

- A network is two or more devices that can share data. The internet is a network of networks.
- Devices are identified two ways:
  - **IP address** (logical, layer 3) — can change depending on the network you join. Public vs private ranges (e.g. `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` are private).
  - **MAC address** (physical, layer 2) — burned into the network card, 48 bits, but can be spoofed.
- **Ping** uses ICMP echo request / echo reply to check if a host is reachable and measure latency.

**Security angle:** MAC spoofing shows why you can't trust a MAC address for access control (e.g. hotel Wi-Fi that only allows "paid" MACs).

## 2. Intro to LAN

- **Topologies:** star (everything to a central switch — most common), bus (one shared cable, single point of failure), ring (data travels around a loop).
- **Switch vs router:** a switch forwards frames inside a LAN using MAC addresses; a router connects different networks and forwards packets using IP addresses.
- **Subnetting:** splitting a network into smaller ones. Each subnet has a *network address*, *host addresses* and a *default gateway*.
- **ARP** maps an IP to a MAC address on the local network (ARP request broadcast → ARP reply).
- **DHCP** hands out IP addresses automatically: Discover → Offer → Request → ACK (DORA).

**Security angle:** ARP has no authentication, which is what makes ARP spoofing / man-in-the-middle attacks possible on a LAN.

## 3. OSI Model

| # | Layer | What happens | Examples |
|---|-------|--------------|----------|
| 7 | Application | Interface for user programs | HTTP, DNS, SMTP |
| 6 | Presentation | Format, encoding, encryption | TLS/SSL, character encoding |
| 5 | Session | Opens and keeps a session alive | session IDs |
| 4 | Transport | Splits data into segments, reliability | TCP, UDP, ports |
| 3 | Network | Logical addressing and routing | IP, ICMP, routers |
| 2 | Data link | Physical addressing, frames | MAC, Ethernet, switches |
| 1 | Physical | Bits on the wire | cables, radio |

**Security angle:** attacks map to layers — ARP spoofing (2), IP spoofing (3), SYN flood (4), SQL injection / XSS (7). Knowing the layer tells you where a defence belongs.

## 4. Packets & Frames

- **Encapsulation:** each layer wraps the data with its own header on the way down; the receiver unwraps it on the way up. A *packet* lives at layer 3, a *frame* at layer 2.
- **TCP** is connection-based and reliable. The **three-way handshake**:
  1. `SYN` — client asks to connect
  2. `SYN/ACK` — server agrees
  3. `ACK` — client confirms

  Closing uses `FIN` / `ACK`; `RST` aborts a connection.
- **UDP** is connectionless — no handshake, no delivery guarantee, but faster (DNS, streaming, gaming).
- **Ports** (0–65535) identify the service: 21 FTP, 22 SSH, 53 DNS, 80 HTTP, 443 HTTPS, 445 SMB, 3389 RDP.

**Security angle:** a SYN flood abuses the handshake by sending SYNs and never finishing them. Port scanners like Nmap use the same handshake to tell open, closed and filtered ports apart.

## 5. Extending Your Network

- **Port forwarding** opens a port on the router so an internal service can be reached from the internet.
- **Firewalls** allow or block traffic by IP, port and protocol. *Stateless* firewalls check each packet alone; *stateful* firewalls track the whole connection.
- **VPN** creates an encrypted tunnel between networks over the internet (PPTP — old and weak, IPSec — strong but harder to set up).
- **Layer 3 switches** can route between VLANs as well as switch.

**Security angle:** every forwarded port is attack surface. A firewall is only as good as its rules — "allow any" rules are a common finding in audits.

---

## Useful commands

```bash
ping -c 4 <host>        # ICMP reachability
ip a / ipconfig         # my IP and MAC
arp -a                  # ARP table
traceroute <host>       # path through routers
```

## What I took from it

This module is the base for everything else: you can't scan, attack or defend a network you can't read. It lines up with my RCI course (Cisco CCNA: Introduction to Networks) at university.
