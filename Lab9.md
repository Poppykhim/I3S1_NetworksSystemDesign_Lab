<p>
  <img src="./Image/ITC.gif" alt="Alt text" style="float: left; width: 220px; margin-right: 20px;">
Name: Virak Rith

Student ID: P20230033

Course: Networks System Design

Instructor: KUY Movsun

Assignment: Lab9( Network Layer Data Plane )

Due Date: December 30, 2025 (12:00 AM)

</p>
<br/>

## Part 1: Topology & Addressing

### Device Table

| Device Name  | Model     | Interface | Description                         |
| ------------ | --------- | --------- | ----------------------------------- |
| ISP-Router   | ISR 4331  | Gig0/0/0  | WAN Link (to Home-Gateway)          |
|              |           | Gig0/0/1  | Internet Link (to Server)           |
| Home-Gateway | ISR 4331  | Gig0/0/0  | WAN Uplink (to ISP)                 |
|              |           | Gig0/0/1  | LAN Gateway (to Switch)             |
| LAN-Switch   | 2960      | Any       | Layer 2 Switch                      |
| Public-DNS   | Server-PT | NIC       | Simulated Internet Server (8.8.8.8) |

### Cabling Guide

- ISP-Router (G0/0/0) ↔ Home-Gateway (G0/0/0): Copper Crossover (or Straight-through if Auto-MDIX)
- ISP-Router (G0/0/1) ↔ Public-DNS: Copper Crossover
- Home-Gateway (G0/0/1) ↔ LAN-Switch (G0/1): Copper Straight-through
- LAN-Switch ↔ PC-A, PC-B, PC-C: Copper Straight-through

<img src="./Image/image.png">

## Part 2: Pre-Lab Setup (Instructor Configuration)

### Configure Public-DNS Server

1. Go to **Public-DNS > Desktop > IP Configuration**
2. Set:
   - IP Address: `8.8.8.8`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `8.8.8.1`

### Configure ISP-Router (CLI)

```bash
Router> enable
Router# configure terminal
Router(config)# hostname ISP-Router

! Connection to Server
Router(config)# interface g0/0/1
Router(config-if)# ip address 8.8.8.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Connection to Home-Gateway
Router(config)# interface g0/0/0
Router(config-if)# ip address 203.0.113.1 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# end
```

 <img src="./Image/image copy.png">

---

## Task 3: Implementing NAT (Network Address Translation)

### Scenario

Convert the LAN to a private network (`192.168.1.0/24`) and configure NAT Overload (PAT) so internal hosts can access the Internet.

---

### Step 3A: Configure WAN Link & Default Route

```bash
Home-Gateway(config)# interface g0/0/0
Home-Gateway(config-if)# description WAN-Link
Home-Gateway(config-if)# ip address 203.0.113.2 255.255.255.252
Home-Gateway(config-if)# no shutdown
Home-Gateway(config-if)# exit

! Default route to ISP
Home-Gateway(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

### Step 3B: Re-configure LAN as Private Network

```bash
! Change LAN Gateway IP
Home-Gateway(config)# interface g0/0/1
Home-Gateway(config-if)# ip address 192.168.1.1 255.255.255.0
Home-Gateway(config-if)# exit

! Update DHCP Pool
Home-Gateway(config)# no ip dhcp pool LAN_POOL
Home-Gateway(config)# ip dhcp pool PRIVATE_LAN
Home-Gateway(dhcp-config)# network 192.168.1.0 255.255.255.0
Home-Gateway(dhcp-config)# default-router 192.168.1.1
Home-Gateway(dhcp-config)# dns-server 8.8.8.8
Home-Gateway(dhcp-config)# exit
```

### Step 3C: Force PCs to renew IP

On PC-A, PC-B, and PC-C: Open the IP Configuration window, switch to Static, wait a second, then switch back to DHCP. Verify they now have an address like 192.168.1.2.

<img src="./Image/image copy 6.png" >

### Step 3D: Configure NAT Overload (PAT)

```bash
! Define Inside and Outside interfaces
Home-Gateway(config)# interface g0/0/1
Home-Gateway(config-if)# ip nat inside
Home-Gateway(config-if)# exit

Home-Gateway(config)# interface g0/0/0
Home-Gateway(config-if)# ip nat outside
Home-Gateway(config-if)# exit

! Create ACL for LAN traffic
Home-Gateway(config)# access-list 1 permit 192.168.1.0 0.0.0.255

! Apply NAT Overload
Home-Gateway(config)# ip nat inside source list 1 interface g0/0/0 overload

```

- Verification: Open Command Prompt on PC-A. Ping 8.8.8.8.
  If configured correctly, the ping will succeed.

<img src="./Image/image copy 3.png">

- Check the router table: show ip nat translations.

<img src="./Image/image copy 4.png">

##

```bash
! Enable IPv6 routing
Home-Gateway(config)# ipv6 unicast-routing

! Configure IPv6 on LAN interface
Home-Gateway(config)# interface g0/0/1
Home-Gateway(config-if)# ipv6 address 2001:DB8:ACAD:1::1/64
Home-Gateway(config-if)# no shutdown
Home-Gateway(config-if)# exit

```

- Verification: On PC-C > IP Configuration, look at the "IPv6 Configuration" section. Ensure it is set to Auto Config. It should automatically generate an IPv6 address.

<img src="./Image/image copy 5.png">
