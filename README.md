# Cisco Packet Tracer - Skills Integration Challenge

This repository contains the complete solution, updated Addressing Table, VLSM subnetting calculations, device CLI configurations, and verification steps for the **Skills Integration Challenge** Packet Tracer activity.

---

## 🌐 Network Topology & Scenario Overview

The scenario required designing a scalable IPv4 addressing scheme using **VLSM** starting from the `192.168.0.0/24` network to accommodate four distinct subnets (Staff, Sales, IT, and a future Guest subnet). Additionally, the project required securing router **R1** and switches **S1, S2, S3**, enabling **SSH remote access**, configuring **IPv4/IPv6 Dual-Stack** addressing across all subnets, and verifying full end-to-end connectivity.

---

## 📋 Complete Addressing Table

| Device | Interface | IPv4 Address / Prefix | IPv6 Unicast / Prefix | IPv6 Link-Local | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R1** | G0/0 | `192.168.0.1/25` | `2001:db8:acad::1/64` | `fe80::1` | N/A |
| **R1** | G0/1 | `192.168.0.129/26` | `2001:db8:acad:1::1/64` | `fe80::1` | N/A |
| **R1** | G0/2 | `192.168.0.193/27` | `2001:db8:acad:2::1/64` | `fe80::1` | N/A |
| **R1** | S0/0/1 | `172.16.1.2/30` | `2001:db8:2::1/64` | `fe80::1` | N/A |
| **Central** | S0/0/0 | `209.165.200.226/30` | `2001:db8:1::1/64` | `fe80::2` | N/A |
| **Central** | S0/0/1 | `172.16.1.1/30` | `2001:db8:2::2/64` | `fe80::2` | N/A |
| **S1** | VLAN 1 | `192.168.0.2/25` | N/A | N/A | `192.168.0.1` |
| **S2** | VLAN 1 | `192.168.0.130/26` | N/A | N/A | `192.168.0.129` |
| **S3** | VLAN 1 | `192.168.0.194/27` | N/A | N/A | `192.168.0.193` |
| **Staff PC** | NIC | `192.168.0.3/25` | `2001:db8:acad::2/64` | `fe80::2` | `192.168.0.1` / `fe80::1` |
| **Sales PC** | NIC | `192.168.0.131/26` | `2001:db8:acad:1::2/64` | `fe80::2` | `192.168.0.129` / `fe80::1` |
| **IT PC** | NIC | `192.168.0.195/27` | `2001:db8:acad:2::2/64` | `fe80::2` | `192.168.0.193` / `fe80::1` |
| **Web Server**| NIC | `64.100.0.3/29` | `2001:db8:cafe::3/64` | `fe80::3` | `64.100.0.1` / `fe80::1` |
| **DNS Server**| NIC | `64.100.0.2/29` | `2001:db8:cafe::2/64` | N/A | `64.100.0.1` / `fe80::1` |

---

## 📊 VLSM Subnet Breakdown (`192.168.0.0/24`)

| Department / Subnet | Needed Hosts | Subnet Mask | Network Address | Usable IP Range | Broadcast Address |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Staff Network** | 100 | `/25` (255.255.255.128) | `192.168.0.0` | `192.168.0.1` - `192.168.0.126` | `192.168.0.127` |
| **Sales Network** | 50 | `/26` (255.255.255.192) | `192.168.0.128` | `192.168.0.129` - `192.168.0.190` | `192.168.0.191` |
| **IT Network** | 25 | `/27` (255.255.255.224) | `192.168.0.192` | `192.168.0.193` - `192.168.0.222` | `192.168.0.223` |
| **Guest Network (Future)**| 25 | `/27` (255.255.255.224) | `192.168.0.224` | `192.168.0.225` - `192.168.0.254` | `192.168.0.255` |

---

## ⚙️ Device Configurations Applied

### 1. Router R1 Configuration Commands

```text
enable
configure terminal
hostname R1
no ip domain-lookup

! Password Security Settings
security passwords min-length 10
enable secret Ciscoenpa55
service password-encryption
banner motd # Unauthorized access is prohibited! #

! Login Security Block
login block-for 180 attempts 4 within 120

! Interface Configurations (Dual-Stack)
interface GigabitEthernet0/0
 description Staff Network
 ip address 192.168.0.1 255.255.255.128
 ipv6 address 2001:db8:acad::1/64
 ipv6 address fe80::1 link-local
 no shutdown

interface GigabitEthernet0/1
 description Sales Network
 ip address 192.168.0.129 255.255.255.192
 ipv6 address 2001:db8:acad:1::1/64
 ipv6 address fe80::1 link-local
 no shutdown

interface GigabitEthernet0/2
 description IT Network
 ip address 192.168.0.193 255.255.255.224
 ipv6 address 2001:db8:acad:2::1/64
 ipv6 address fe80::1 link-local
 no shutdown

! SSH & Console Access
ip domain-name CCNA-lab.com
crypto key generate rsa modulus 1024
username Admin1 privilege 15 secret Admin1pa55

line console 0
 password Ciscoconpa55
 login
 exec-timeout 5 0

line vty 0 4
 transport input ssh
 login local
 exec-timeout 5 0
```

### 2. Switch S1 Configuration Commands (S2 & S3 followed identical structure with their respective IPs)

```text
enable
configure terminal
hostname S1
no ip domain-lookup

enable secret Ciscoenpa55
service password-encryption

interface VLAN 1
 ip address 192.168.0.2 255.255.255.128
 no shutdown

ip default-gateway 192.168.0.1

line console 0
 password Ciscoconpa55
 login
 exec-timeout 5 0

line vty 0 15
 password Ciscoconpa55
 login
 exec-timeout 5 0
```

---

## ✅ Connectivity Verification

* **Dual-Stack Pings:** Verified successful ICMP reachability across all local hosts, default gateways, and external server addresses (`64.100.0.2`, `64.100.0.3`, `2001:db8:cafe::2`, `2001:db8:cafe::3`).
* **Web Services:** Navigated successfully from departmental browser clients to `www.cisco.srv` (IPv4) and `www.cisco6.srv` (IPv6).
* **SSH Management:** Tested encrypted SSH access to Router R1 using `Admin1` user profile from host devices.