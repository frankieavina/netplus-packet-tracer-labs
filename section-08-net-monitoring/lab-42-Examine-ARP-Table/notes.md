
# Packet Tracer - Examine the ARP Table

## Addressing Table

| Device | Interface | MAC Address | Switch Interface |
|---|---|---|---|
| Router0 | G0/0 | 0001.6458.2501 | G0/1 |
| Router0 | S0/0/0 | N/A | N/A |
| Router1 | G0/0 | 00E0.F7B1.8901 | G0/1 |
| Router1 | S0/0/0 | N/A | N/A |
| 10.10.10.2 | Wireless | 0060.2F84.4AB6 | F0/2 |
| 10.10.10.3 | Wireless | 0060.4706.572B | F0/2 |
| 172.16.31.2 | F0 | 000C.85CC.1DA7 | F0/1 |
| 172.16.31.3 | F0 | 0060.7036.2849 | F0/2 |
| 172.16.31.4 | G0 | 0002.1640.8D75 | F0/3 |

## Topology

```
Router0 ~~~~(serial)~~~~ Router1
   |                        |
Switch0                  Switch1
   |                    /   |   \
Access Point       172.16.31.2 .3  .4
  /      \
10.10.10.2  10.10.10.3 (wireless)
```

Wired LAN: `172.16.31.0/24` (behind Router1). Wireless LAN: `10.10.10.0/24` (behind Router0).

<img width="472" height="358" alt="Screenshot 2026-10-14 at 2 55 01 PM" src="https://github.com/user-attachments/assets/20baae81-15b4-46b0-a408-7f1772e18dcb" />

## Objectives

- **Part 1:** Examine an ARP Request
- **Part 2:** Examine a Switch MAC Address Table
- **Part 3:** Examine the ARP Process in Remote Communications

## Background

This activity is optimized for viewing PDUs. The devices are already configured. You will gather PDU information in **simulation mode** and answer a series of questions about the data you collect.

---

## Key Concepts (read this first)

| Concept | What to remember |
|---|---|
| **ARP** | Maps a known **IP address** to an unknown **MAC address** on the same network |
| **ARP request** | Sent as a **broadcast** (destination MAC `FFFF.FFFF.FFFF`), so every device on the LAN receives it |
| **ARP reply** | Sent as a **unicast** straight back to the requester |
| **Switch flooding** | A switch sends a broadcast out **every port except the one it arrived on** |
| **Switch learning** | A switch records each frame's **source MAC** against the **port** it came in on |
| **Remote destinations** | A host never ARPs for a remote IP. It ARPs for its **default gateway** and sends the frame to the gateway's MAC |

---

## Part 1: Examine an ARP Request

### Step 1: Generate ARP requests by pinging 172.16.31.3 from 172.16.31.2

- [ ] Click **172.16.31.2 → Desktop → Command Prompt**.
- [ ] Clear the ARP table:
  ```
  C:\> arp -d
  ```
- [ ] Switch to **Simulation mode**.
- [ ] Ping the other PC:
  ```
  C:\> ping 172.16.31.3
  ```
  Two PDUs appear: an ICMP echo and an ARP request. The ICMP packet can't be sent until the PC knows the destination's MAC address, so the PC broadcasts an ARP request first.
- [ ] Click **Capture/Forward** once. The ARP PDU moves to Switch1, and the ICMP PDU disappears while it waits for the ARP reply.
- [ ] Open the ARP PDU and record the **destination MAC address**.

> **Q: Is this address listed in the table above?**
> **A : No.** The destination is the broadcast address `FFFF.FFFF.FFFF`, which isn't any one device's MAC.

> **Q: How many copies of the PDU did Switch1 make?**
> **A : 3.** The switch floods the broadcast out every port except the one it arrived on: F0/2 (172.16.31.3), F0/3 (172.16.31.4), and G0/1 (Router1).

> **Q: What is the IP address of the device that accepted the PDU?**
> **A : 172.16.31.3.** It's the ARP target. The other devices receive the broadcast but discard it because the target IP isn't theirs.

- [ ] Open the PDU and examine **Layer 2**.

> **Q: What happened to the source and destination MAC addresses?**
> **A : They swapped roles.** In the ARP reply, the source is 172.16.31.3's MAC (`0060.7036.2849`) and the destination is 172.16.31.2's MAC (`000C.85CC.1DA7`). The broadcast destination from the request is replaced by a specific unicast address.

- [ ] Keep clicking **Capture/Forward** until the PDU returns to 172.16.31.2.

> **Q: How many copies of the PDU did the switch make during the ARP reply?**
> **A : 1.** The reply is a unicast, and the switch already learned 172.16.31.2's MAC from the request, so it forwards out only F0/1.

### Step 2: Examine the ARP table

- [ ] The ICMP packet reappears. Open it and examine the MAC addresses.

> **Q: Do the MAC addresses of the source and destination align with their IP addresses?**
> **A: Yes.** Source MAC `000C.85CC.1DA7` belongs to 172.16.31.2, and destination MAC `0060.7036.2849` belongs to 172.16.31.3.

- [ ] Switch back to **Realtime** and let the ping complete.
- [ ] On 172.16.31.2, run:
  ```
  C:\> arp -a
  ```

> **Q: To what IP address does the MAC address entry correspond?**
> **A : 172.16.31.3** (MAC `0060.7036.2849`).

> **Q: In general, when does an end device issue an ARP request?**
> **A:** When it needs to send a frame to a destination IP address on its **own network** and doesn't have that destination's MAC address in its ARP table.

---

## Part 2: Examine a Switch MAC Address Table

### Step 1: Generate additional traffic to populate the switch MAC address tables

- [ ] From 172.16.31.2:
  ```
  C:\> ping 172.16.31.4
  ```
- [ ] Click **10.10.10.2 → Desktop → Command Prompt**:
  ```
  C:\> ping 10.10.10.3
  ```

> **Q: How many replies were sent and received?**
> **A:** Packet Tracer sends **4** echo requests per ping, so expect up to **4 replies** from each. The first ping can occasionally time out while ARP completes, giving 3 replies.

### Step 2: Examine the MAC address table on the switches

- [ ] Click **Switch1 → CLI** tab:
  ```
  Switch1> enable
  Switch1# show mac-address-table
  ```

> **Q: Do the entries correspond to those in the table above?**
> **A: Yes.** I see `000C.85CC.1DA7` on F0/1, `0060.7036.2849` on F0/2, and `0002.1640.8D75` on F0/3. A switch only lists devices it has seen send a frame, so Router1's MAC (`00E0.F7B1.8901` on G0/1) appears once it has sent traffic.

- [ ] Click **Switch0 → CLI** tab:
  ```
  Switch0> enable
  Switch0# show mac-address-table
  ```

> **Q: Do the entries correspond to those in the table above?**
> **A: Yes.** Both wireless MACs (`0060.2F84.4AB6` and `0060.4706.572B`) are listed against **F0/2**.

> **Q: Why are two MAC addresses associated with one port?**
> **A:** Both wireless laptops connect through the **same access point**, which is plugged into Switch0's F0/2. The AP forwards both laptops' frames out that single port, so the switch learns two source MACs on it.

---

## Part 3: Examine the ARP Process in Remote Communications

### Step 1: Generate traffic to produce ARP traffic

- [ ] Click **172.16.31.2 → Desktop → Command Prompt**:
  ```
  C:\> ping 10.10.10.1
  C:\> arp -a
  ```

> **Q: What is the IP address of the new ARP table entry?**
> **A: 172.16.31.1**, the **default gateway** (Router1's G0/0, MAC `00E0.F7B1.8901`). The PC never learns the MAC of the remote address `10.10.10.1`. It learns its gateway's MAC instead.

- [ ] Clear the ARP table and switch to **Simulation mode**:
  ```
  C:\> arp -d
  ```
- [ ] Repeat the ping:
  ```
  C:\> ping 10.10.10.1
  ```

> **Q: How many PDUs appear?**
> **A: 2**, an ICMP echo and an ARP request (same as Part 1).

- [ ] Click **Capture/Forward**, then click the PDU now at Switch1.

> **Q: What is the target destination IP address of the ARP request?**
> **A (expected): 172.16.31.1**, the default gateway.

> **Q: The destination IP address is not 10.10.10.1. Why?**
> **A:** `10.10.10.1` is on a **different network**, so the PC can't reach it directly by MAC address. It ARPs for its **default gateway** and sends the frame there. The router then routes the packet toward the remote network.

### Step 2: Examine the ARP table on Router1

- [ ] Switch to **Realtime**. Click **Router1 → CLI** tab:
  ```
  Router1> enable
  Router1# show mac-address-table
  ```

> **Q: How many MAC addresses are in the table? Why?**
> **A: None** (the command may be rejected or return an empty table). Routers don't keep MAC address tables; that's a **switch** function. Routers forward based on IP routes and use an **ARP table** to find MACs.

- [ ] Now check the ARP table:
  ```
  Router1# show arp
  ```

> **Q: Is there an entry for 172.16.31.2?**
> **A: Yes.** Router1 learned it from the ARP request 172.16.31.2 sent for its gateway address.

> **Q: What happens to the first ping in a situation where the router responds to the ARP request?**
> **A:** The **first ping typically times out**. Time is spent on the ARP exchange before the packet can be forwarded. Subsequent pings succeed because the ARP entries are now cached.

---

## Summary: What Makes ARP Different Locally vs. Remotely

| | Destination on same LAN | Destination on a remote network |
|---|---|---|
| ARP target | The destination's IP | The **default gateway's** IP |
| Frame destination MAC | Destination's MAC | **Gateway's MAC** |
| Packet destination IP | Destination's IP | Destination's IP (unchanged) |

## What I Learned

- **Why ARP requests are broadcasts but replies are unicasts:**
- **How a switch builds its MAC table:**
- **Why a PC ARPs for its gateway instead of a remote host:**

