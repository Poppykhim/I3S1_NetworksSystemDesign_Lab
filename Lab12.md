<p>
  <img src="./Image/ITC.gif" alt="Alt text" style="float: left; width: 220px; margin-right: 20px;">
Name: Virak Rith

Student ID: P20230033

Course: Networks System Design

Instructor: KUY Movsun

Assignment: Lab12 ( The Link Layer and LANs )

Due Date: January, 27 2026, (12:00 AM)

</p>
<br/>

# Part 1: Configuring Port-Based VLANs and Traffic Isolation

- Build the Topology: Connect four PCs to a 2960 Switch using ports Fa0/1 through Fa0/4:

![alt text](image-2.png)

- Create VLANs: Access the Switch CLI and create two VLANs:

```css
Switch>enable
Switch#config t
Switch(config)#vlan 10
Switch(config-vlan)#name Engineering
Switch(config-vlan)#vlan 20
Switch(config-vlan)#name Marketing
Switch(config-vla)#exit
```

- Assign Ports:

```css
Switch(config)#interface range fa0/1 - 2
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#exit
Switch(config)#interface range fa0/3 - 4
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 20
```

- Test Connectivity:

![alt text](/Image/image-1.png)

---

# Part 2: Multi-Switch VLANs and 802.1Q Trunking

- Switch 2:

```css
Switch>enable
Switch#config t
Switch(config)#interface gig0/1
Switch(config-if)#switchport mode trunk

Switch(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
```

- Switch 1:

```css
Switch>enable
Switch#config t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#interface gig0/1
Switch(config-if)#switchport mode trunk
```

- Inspect the PDU details on the trunk link to find the 802.1Q Tag and the VLAN ID.

![alt text](/Image/image-3.png)

---

# Part 3: Inter-VLAN Routing (Router-on-a-Stick)

- Router:

```css
Router>enable
Router#
Router#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#interface GigabitEthernet0/0
Router(config-if)#exit
Router(config)#interface g0/0
Router(config-if)#no shutdown

Router(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

Router(config-if)#exit
Router(config)#interface g0/0.10
Router(config-subif)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/0.10, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.10, changed state to up

Router(config-subif)#encapsulation dot1q 10
Router(config-subif)#ip address 192.168.10.1 255.255.255.0
Router(config-subif)#exit
Router(config)#interface g0/0.20
Router(config-subif)#
%LINK-3-UPDOWN: Interface GigabitEthernet0/0.20, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.20, changed state to up

Router(config-subif)#encapsulation dot1q 20
Router(config-subif)#ip address 192.168.20.1 255.255.255.0
Router(config-subif)#
Router#
```

- Switch 0:

```css
Switch>enable
Switch#config t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#interface gig0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/5, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to up

Switch(config-if)#exit
Switch(config)#
%LINK-3-UPDOWN: Interface FastEthernet0/5, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up

Switch(config)#interface fa0/24
Switch(config-if)#switchport mode trunk

Switch(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/24, changed state to up

Switch(config-if)#exit
Switch(config)#interface GigabitEthernet0/1
Switch(config-if)#exit
Switch(config)#interface gig0/1
Switch(config-if)#switchport mode trunk
```

- switch 1:

![alt text](/Image/image-4.png)

![alt text](/Image/image-5.png)

# IP config:

![alt text](/Image/image-6.png)

![alt text](/Image/image-7.png)

![alt text](/Image/image-8.png)

![alt text](/Image/image-9.png)

![alt text](/Image/image-10.png)

![alt text](/Image/image-11.png)

![alt text](/Image/image-12.png)

![alt text](/Image/image-13.png)

---

# Part 4: Synthesis Challenge - "A Day in the Life"

![alt text](/Image/image-14.png)

![alt text](/Image/image-15.png)

![alt text](/Image/image-16.png)

1. Router-on-Stick & Trunk Mode:

A single physical connection can be used to transport traffic from multiple VLANs to a router. By using trunk mode with 802.1Q tagging, VLAN information is preserved in each frame, allowing the router to identify the source VLAN and perform inter-VLAN routing correctly.

2. 12-bit VLAN ID Purpose:

The 12-bit VLAN ID field in an 802.1Q frame is used to uniquely label VLANs in the range of 1 to 4094. This identifier allows switches and routers to recognize which VLAN a frame belongs to, ensuring proper traffic separation and forwarding.

3. VLANs & Broadcast Domain Reduction:

VLANs divide a network into multiple broadcast domains. Broadcast traffic is restricted to the VLAN in which it originates and is not forwarded to other VLANs, which helps reduce unnecessary broadcast traffic and improves overall network efficiency.
