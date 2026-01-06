<p>
  <img src="./Image/ITC.gif" alt="Alt text" style="float: left; width: 220px; margin-right: 20px;">
Name: Virak Rith

Student ID: P20230033

Course: Networks System Design

Instructor: KUY Movsun

Assignment: Lab10 ( Network Layer Control Plane Algorithms )

Due Date: January, 13 2026, (12:00 AM)

</p>
<br/>

# Part1: The Link State Walkthrough (Dijkstra)

| Step | N' (Nodes added) | D(B), p(B) | D(C), p(C) | D(D), p(D) | D(E), p(E) | D(F), p(F) |
| ---- | ---------------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| 0    | A                | 2, A       | 1, A       | ∞          | ∞          | ∞          |
| 1    | A, C             | 2, A       | 1, A       | 3, C       | 5, C       | ∞          |
| 2    | A, C, B          | 2, A       | 1, A       | 3, C       | 5, C       | ∞          |
| 3    | A, C, B, D       | 2, A       | 1, A       | 3, C       | 4, D       | 8, D       |
| 4    | A, C, B, D, E    | 2, A       | 1, A       | 3, C       | 4, D       | 5, E       |
| 5    | A, C, B, D, E, F | 2, A       | 1, A       | 3, C       | 4, D       | 5, E       |

## Final answer

```
Path to F: A → C → D → E → F

Total cost: 5
```

# Part 2: Count-to-Infinity Simulation

### Initial State

| Router | Cost to Z | Next Hop |
| ------ | --------- | -------- |
| Y      | 1         | Direct   |
| X      | 5         | Y        |

---

### Iteration 1

| Router | Update Action                                            | New Cost to Z | Next Hop |
| ------ | -------------------------------------------------------- | ------------- | -------- |
| Y      | Detects link change (60). Chooses route via X: 4 + 5 = 9 | 9             | X        |
| X      | Receives Y’s update (9). Updates: 4 + 9 = 13             | 13            | Y        |

---

### Iteration 2

| Router | Update Action                                  | New Cost to Z | Next Hop |
| ------ | ---------------------------------------------- | ------------- | -------- |
| Y      | Receives X’s update (13). Updates: 4 + 13 = 17 | 17            | X        |
| X      | Receives Y’s update (17). Updates: 4 + 17 = 21 | 21            | Y        |

---

### Explanation

- Costs keep increasing step by step toward infinity.
- This slow convergence is why the phenomenon is called **“Bad news travels slow”** in Distance Vector routing.

# Part 3: Packet Tracer Activity (RIP)

## Objective

Build a 3‑hop network and verify how RIP (a Distance Vector protocol) handles hop counts.

---

## 1. Setup Topology

Logical map:

- Devices: Cisco 2911 Routers
- Connections: GigabitEthernet cables

<img src="./Image/image.png">

---

## 2. IP Configuration

| Router | Interface | IP Address  | Subnet Mask   |
| ------ | --------- | ----------- | ------------- |
| R0     | G0/0      | 192.168.1.1 | 255.255.255.0 |
| R1     | G0/0      | 192.168.1.2 | 255.255.255.0 |
| R1     | G0/1      | 192.168.2.1 | 255.255.255.0 |
| R2     | G0/0      | 192.168.2.2 | 255.255.255.0 |
| R2     | Loopback0 | 10.10.10.1  | 255.255.255.0 |

Loopback creation:

```bash
Router(config)# interface loopback 0
Router(config-if)# ip address 10.10.10.1 255.255.255.0
```

## 3. RIP Configuration

```
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# no auto-summary
Router(config-router)# network 192.168.1.0
Router(config-router)# network 192.168.2.0
Router(config-router)# network 10.0.0.0 (only on Router2)
```

## 4. Verification

#### 1. Run show ip route. Look for the line starting with R (RIP) for destination 10.0.0.0/8.

<img src="./Image/image copy.png">

#### 2. What does the 120 represent? What does the 2 represent?

- **120** is the **Administrative Distance (AD)** of RIP.

  - It defines the trust level of the routing source.
  - RIP has a default AD of 120, which is relatively low priority compared to other protocols like OSPF (110) or EIGRP (90).

- **2** is the **hop count** to reach the destination network.
  - It means there are **2 routers** between the current router and the destination `10.10.10.0/24`.
  - This matches the topology: Router0 → Router1 → Router2.

---

### 3. How many lines of output do you see in traceroute? Does this match the metric?

<img src="./Image/image copy 2.png">

- The `traceroute 10.10.10.1` command shows **2 lines of output**:

1. First hop: `192.168.1.2` (Router1)
2. Second hop: `192.168.2.2` (Router2)

- Yes, this matches the RIP metric of **2 hops** shown in the routing table `[120/2]`.

