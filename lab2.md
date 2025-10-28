<p>
  <img src="./Image/ITC.gif" alt="Alt text" style="float: left; width: 220px; margin-right: 20px;">
Name: Virak Rith

Student ID: P20230033

Course: Networks System Design

Instructor: KUY Movsun

Assignment: Lab1

Due Date: October 28, 2025 (12:00 AM)

</p>
<br/>

## Part B. Packet Tracer Measurement

1. Create a simple topology: PC0 — Switch — PC1 with straight-through cables.
2. ign IPs: PC0 192.168.1.1/24, PC1 192.168.1.2/24.
3. Test connectivity from PC0 using ping 192.168.1.2.
4. Switch to Simulation Mode → use the Simple PDU (envelope icon) from PC0 → PC1.
5. Open the Event List and record the time when the Echo Request leaves PC0 and when the Echo Reply returns to PC0 (this is your RTT).
6. Assume each Simple PDU ≈ 600 bits (typical ICMP ping).
7. Calculate throughput using Throughput (bps) = Packet Size (bits) / RTT (seconds).

   | Test | Packet Size (bits) | RTT (s) | Throughput (bps) | Loss (%) |
   | ---- | ------------------ | ------- | ---------------- | -------- |
   | 1    | 600                | 0.004   | 150,000          | 0        |
   | 2    | 600                | 0.004   | 150,000          | 0        |
