<p>
  <img src="./Image/ITC.gif" alt="Alt text" style="float: left; width: 220px; margin-right: 20px;">
Name: Virak Rith

Student ID: P20230033

Course: Networks System Design

Instructor: KUY Movsun

Assignment: Lab10 ( The Link Layer and LANs )

Due Date: January, 20 2026, (12:00 AM)

</p>
<br/>

# Part 1: Manual Calculation

## Exercise 1: Cyclic Redundancy Check (CRC)

### Concept

CRC is used by network hardware to detect bit errors in transmitted frames. The sender computes CRC bits **R** such that the combined bit pattern ⟨D, R⟩ is exactly divisible by the generator polynomial **G** using modulo-2 arithmetic (XOR).

---

### Given

- **Data Packet (D)** = `101010` (6 bits)
- **Generator Polynomial (G)** = `1001` (4 bits)
- **CRC length (r)** = length(G) − 1 = **3 bits**

---

### Step 1: Append Zeros to Data

Append 3 zeros to the data packet: 101010000

---

### Step 2: Modulo-2 Division

Perform binary (XOR) division of `101010000` by `1001`.

**Resulting remainder:**
R = 111

---

### Step 3: Transmitted Bit Pattern

Append the remainder to the original data:
<D, R> = 101010111

---

### Step 4: Verification

Divide the transmitted pattern `101010111` by the generator `1001`.
Remainder = 000

✔ The remainder is zero, so the frame is valid and error-free.

---

### Step 5: Simulate an Error

Flip the last bit of the transmitted pattern:

- Original frame: 101010111
- Error frame: 101010110

Divide the erroneous frame by `1001`:Remainder ≠ 000

---

### Analysis (CRC)

- The remainder is **non-zero**, meaning an error is detected.
- The receiver uses this result to **discard the frame** and rely on higher-layer protocols for retransmission.
- This is how CRC enables reliable error detection in hardware.

---

## Exercise 2: CSMA/CD Exponential Backoff

### Concept

Ethernet uses CSMA/CD with binary exponential backoff to reduce repeated collisions. After the m-th collision, a node chooses a random value:

\[
K \in \{0, 1, 2, \dots, 2^m - 1\}
\]

The node waits **K × 512 bit times** before retransmitting.

---

### Given

- Collision count: **m = 3**
- Chosen random value: **K = 4**
- Ethernet speed: **10 Mbps**
- 1 bit time = **0.1 μs**

---

### Step 1: Collision Window

\[
2^3 - 1 = 7
\]

Possible values of **K**: {0, 1, 2, 3, 4, 5, 6, 7}

---

### Step 2: Wait Time in Bit Times

\[
4 × 512 = 2048 \text{ bit times}
\]

---

### Step 3: Convert to Real Time

\[
2048 × 0.1 μs = 204.8 μs
\]

**Total wait time before retransmission:** 204.8 microseconds

---

### Analysis (CSMA/CD)

- As **m increases**, the random backoff range becomes larger.
- This spreads retransmission attempts over time.
- It reduces repeated collisions and helps **relieve network congestion** when traffic is heavy.

---

# Activity A: Shared Media and Collisions with Hubs (Part 2)

## Topology Setup

- **Devices Used:**

  - PC0, PC1, PC2, PC3
  - Hub0
  - Switch0

- **Connections:**

  - PC0 and PC1 connected to Hub0 using Copper Straight-Through cables
  - PC2 and PC3 not connected

- **Simulation Mode:**

  - Switch to Simulation Mode (Shift + S)
  - Open "Edit Filters" and enable only **ICMP**

- **Traffic Generation:**
  - Use "Add Simple PDU" tool: - PC0 → PC1 - PC1 → PC0

<img src="./Image/image.png">

- **Observation:**
  - Step through simulation using "Play" or "Forward"
  - Watch for collision icon at Hub0 (fire or red X)

---

## Event List Summary

| Time (sec) | Last Device | At Device | Packet Type |
| ---------- | ----------- | --------- | ----------- |
| 0.000      | —           | PC1       | ICMP        |
| 0.000      | —           | PC0       | ICMP        |
| 0.002      | Hub0        | PC0       | ARP         |
| 0.002      | Hub0        | PC1       | ARP         |
| 2.001      | —           | PC1       | ICMP        |
| 2.003      | —           | PC0       | ICMP        |

---

## Analysis Question

### Why does a collision occur when using a Hub?

- **Layer 1 Behavior (Hub):**

  - Operates at the Physical Layer
  - Does not inspect MAC addresses
  - Repeats incoming signals to all ports
  - Creates a **shared collision domain**
  - When two devices transmit simultaneously, signals overlap → **collision**

- **Layer 2 Behavior (Switch):**
  - Operates at the Data Link Layer
  - Learns MAC addresses and forwards frames only to the correct port
  - Each port is its own collision domain
  - **No collisions** occur in full-duplex switch environments

---

## Conclusion

Hubs cause collisions due to their broadcast nature at Layer 1. Switches prevent collisions by intelligently forwarding frames at Layer 2.

# Activity B: Switch MAC Table Learning

## Objective

Understand how switches learn device locations to create **dedicated collision domains**, unlike hubs which broadcast to all devices.

---

## 🔧 Steps

### 1. Re-Connect

- Delete the Hub from the previous activity.
- Connect **PC0, PC1, and PC2** to the Switch.
- Wait for link lights to turn green (or press **Fast Forward Time** in Realtime mode).

### 2. Inspect Empty Table

- Open the Switch CLI.
- Run: show mac-address-table

- The table should be **empty** initially.

<img src="./Image/image copy.png">

### 3. Ping

- Switch to **Simulation Mode**.
- Edit Filters: Ensure **ARP** and **ICMP** are checked.
- Use the **Add Simple PDU** tool to ping from **PC0 → PC1**.

### 4. Step-by-Step Learning

- Click **Forward** until the frame leaves PC0 and reaches the Switch.
- Stop and check the MAC table again: show mac-address-table

- PC0’s MAC address should now be associated with its port (e.g., **Fa0/1**).

  → The switch has **learned PC0’s location**.

<img src="./Image/image copy 2.png">

### 5. Complete the Ping

- Continue forwarding until the reply returns from PC1 to PC0.
- Check the MAC table again:
- PC1’s MAC address should now be learned and associated with its port.

## Analysis Question

**In Activity A (Hub), the packet was broadcast to everyone. In this activity, after the MAC table was populated, did the Switch send the ICMP reply (from PC1) to PC2? Why or why not?**

- **Answer:** No, the Switch did **not** send the ICMP reply to PC2.
- **Reason:** Once the MAC table is populated, the Switch knows which port corresponds to PC0’s MAC address. It forwards the reply **only to PC0’s port**.
- **Key Difference:** Unlike a Hub, which broadcasts frames to all devices, a Switch isolates traffic per port using its MAC table. This prevents collisions and unnecessary broadcasts.

---

## Conclusion

Switches dynamically build a MAC address table by learning source addresses from incoming frames. This allows them to forward traffic only to the correct destination port, creating **collision-free communication** and efficient use of bandwidth.

# Activity C: Switch MAC Table Learning

## Objective

Understand how switches learn MAC addresses to create dedicated collision domains and forward frames intelligently.

---

## Setup Instructions

### 1. Reconnect Devices

- Delete the Hub from previous activities.
- Connect **PC0, PC1, and PC2** to **Switch0** using Copper Straight-Through cables.
- Wait for link lights to turn green or press **Fast Forward Time** in Realtime mode.

### 2. Inspect the Empty MAC Table

- Click on the Switch.
- Go to the **CLI tab**.
- Type: show mac-address-table

- The table should be **empty** initially.

### 3. Generate Traffic

- Switch to **Simulation Mode**.
- Open **Edit Filters** and ensure **ARP** and **ICMP** are checked.
- Use the **Add Simple PDU** tool to ping from **PC0 → PC1**.

---

## Step-by-Step MAC Learning

### 4. Observe Frame Movement

- Click **Forward** until the frame leaves PC0 and reaches the Switch.

### 5. Check MAC Table

- Open the Switch CLI again and run: show mac-address-table

  - You should now see **PC0’s MAC address** associated with its port (e.g., Fa0/1).

### 6. Complete the Ping

- Continue clicking **Forward** until the ICMP reply returns from PC1 to PC0.

### 7. Final MAC Table Check

- Run:show mac-address-table

- You should now see **PC1’s MAC address** added to the table.

---

##  Analysis Question

**In Activity A (Hub), the packet was broadcast to everyone. In this activity, after the MAC table was populated, did the Switch send the ICMP reply (from PC1) to PC2? Why or why not?**

- **Answer:** No, the Switch did **not** send the ICMP reply to PC2.
- **Reason:** The Switch had already learned PC0’s MAC address and its associated port. It forwarded the reply **only to PC0**, not to PC2.
- **Conclusion:** Unlike a Hub, a Switch uses its MAC table to forward frames only to the correct destination, preventing unnecessary broadcasts and collisions.

---

##  Summary

Switches dynamically learn MAC addresses from incoming frames and build a table to forward traffic efficiently. This isolates communication between devices and eliminates collisions, making switches ideal for modern LAN environments.
