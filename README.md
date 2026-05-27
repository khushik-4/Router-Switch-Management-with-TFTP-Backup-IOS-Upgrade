# 🌐 Router & Switch Management with TFTP Backup & IOS Upgrade

<div align="center">

![Cisco](https://img.shields.io/badge/Cisco-Packet%20Tracer%209.0-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![OSPF](https://img.shields.io/badge/Routing-OSPF-orange?style=for-the-badge)
![TFTP](https://img.shields.io/badge/Backup-TFTP-blue?style=for-the-badge)
![SSH](https://img.shields.io/badge/Security-SSH%20v2-green?style=for-the-badge)

**NND Project | Course ID: INITC602 | NSUT Delhi**

| Team Member | Enrollment No. |
|---|---|
| Vansh Bhardwaj | 2023UIN3324 |
| Khushi Kumari | 2023UIN3334 |
| Ishita Soni | 2023UIN3343 |

*Submitted to: Ms. Pooja Yadav*

</div>

---

## 📌 Overview

This project simulates an **enterprise university campus network** in Cisco Packet Tracer 9.0. It features a **3-tier hierarchical topology** with centralized TFTP-based configuration backup, IOS image management, OSPF dynamic routing, inter-VLAN routing, and network security.

## 🖼️ Network Topology

![Network Topology](Images/project.png)

---

## 📋 Table of Contents
- [Network Design](#-network-design)
- [VLAN & IP Scheme](#-vlan--ip-scheme)
- [Key Features](#-key-features)
- [TFTP Backup & IOS Upgrade](#-tftp-backup--ios-upgrade)
- [Security](#-security)
- [Verification](#-verification)
- [Files in this Repo](#-files-in-this-repo)

---

## 🗺️ Network Design

The topology follows a **3-tier hierarchical model**:

```
             [ Edge Router ER-1 ] ← Internet/ISP
                      |
             [ Core Router CR-1 ]
             /                   \
      [ Dist-SW-1 ]          [ Dist-SW-2 ]
    (Admin, IT, Mgmt)     (Labs, Library, Servers)
      /         \              /           \
 [Access-SW-1] [Access-SW-2] [Access-SW-3] [Access-SW-4]
  Admin Dept    IT Dept       Computer Lab   Library
```

| Device | Model | Role |
|---|---|---|
| CR-1 | Cisco 2911 | Core router, OSPF backbone |
| ER-1 | Cisco 2911 | WAN edge, NAT, DHCP |
| Dist-SW-1/2 | Cisco 3560-24PS | L3 switches, inter-VLAN routing |
| Access-SW-1 to 4 | Cisco 2960-24TT | End-user access switches |
| TFTP Server | Server-PT | Config & IOS backup |
| DNS/Web Server | Server-PT | Internal services |

---

## 🏷️ VLAN & IP Scheme

| VLAN | Name | Subnet | Gateway |
|---|---|---|---|
| 10 | Admin | 192.168.10.0/24 | 192.168.10.1 |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Labs | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Library | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Management | 192.168.50.0/24 | 192.168.50.1 |
| 100 | Servers | 192.168.100.0/24 | 192.168.100.1 |

**Key IPs:** TFTP Server `192.168.100.10` · DNS/Web Server `192.168.100.20` · Management PC `192.168.50.10`

---

## ⚡ Key Features

- **Multi-Area OSPF** — Area 0 (backbone), Area 1 (Admin/IT), Area 2 (Labs/Library)
- **Inter-VLAN Routing** via SVIs on L3 distribution switches
- **802.1Q Trunking** between distribution and access switches
- **NAT/PAT** on ER-1 for internet access
- **DHCP** pools configured per VLAN on Dist-SW-1
- **STP PVST+** with optimized root bridge placement per VLAN

---

## 💾 TFTP Backup & IOS Upgrade

### Configuration Backup (all devices)
```bash
# Backup to TFTP
Router# copy running-config tftp
Address or name of remote host? 192.168.100.10
Destination filename? CR-1-running-backup

# Restore from TFTP
Router# copy tftp running-config
Address or name of remote host? 192.168.100.10
Source filename? CR-1-running-backup
```

### IOS Upgrade Process
```bash
# 1. Backup current IOS first
CR-1# copy flash: tftp:
Source filename? c2900-universalk9-mz.SPA.151-4.M4.bin
Destination filename? CR-1-IOS-backup.bin

# 2. Copy new IOS from TFTP
CR-1# copy tftp flash:
Source filename? c2900-universalk9-mz.SPA.155-3.M4a.bin

# 3. Set boot order with fallback
CR-1(config)# boot system flash:c2900-universalk9-mz.SPA.155-3.M4a.bin
CR-1(config)# boot system flash:c2900-universalk9-mz.SPA.151-4.M4.bin
CR-1# reload
```

---

## 🔒 Security

| Feature | Implementation |
|---|---|
| **SSH v2** | All devices — replaces Telnet for remote management |
| **ACL (TFTP-ACCESS)** | Restricts TFTP access to authorized subnets only |
| **Port Security** | Sticky MAC, max 1 address, restrict violation on access ports |
| **STP BPDU Guard** | Enabled globally on all access ports |
| **Password Encryption** | `service password-encryption` + `enable secret` on all devices |

---

## ✅ Verification

```bash
# OSPF
show ip ospf neighbor          # Should show FULL state neighbors
show ip route ospf             # Should show all 192.168.x.0 subnets

# VLANs & Trunks
show vlan brief
show interfaces trunk

# TFTP & Flash
show flash:
show startup-config

# Security
show port-security interface Fa0/2
show access-lists
show ip ssh
```

**Ping Test Results (from Admin-PC1):**
- ✅ Default gateway `192.168.10.1` — 0% loss
- ✅ IT VLAN `192.168.20.10` — reachable
- ✅ TFTP Server `192.168.100.10` — reachable
- ✅ WAN link `10.0.0.2` — 0% loss

---

## 📁 Files in this Repo

```
📦 Router-Switch-Management-with-TFTP/
├── 📄 README.md
├── 📦 Final NND Project.pkt        ← Open in Cisco Packet Tracer 9.0+
├── 📄 NND Project Report (final).pdf
├── 🔑 LICENSE
└── 📁 Images/
    └── project.png                 ← Network topology screenshot
```

> **Requirements:** Cisco Packet Tracer 9.0+, free via [Cisco NetAcad](https://www.netacad.com)

---

<div align="center">
Made with ❤️ by Vansh Bhardwaj, Khushi Kumari & Ishita Soni · NSUT Delhi · B.Tech IT 2023
</div>