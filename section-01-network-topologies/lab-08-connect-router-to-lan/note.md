
# Packet Tracer - Connect a Router to a LAN

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | N/A |
| R1 | G0/1 | 192.168.11.1 | 255.255.255.0 | N/A |
| R1 | S0/0/0 (DCE) | 209.165.200.225 | 255.255.255.252 | N/A |
| R2 | G0/0 | 10.1.1.1 | 255.255.255.0 | N/A |
| R2 | G0/1 | 10.1.2.1 | 255.255.255.0 | N/A |
| R2 | S0/0/0 | 209.165.200.226 | 255.255.255.252 | N/A |
| PC1 | NIC | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 | NIC | 192.168.11.10 | 255.255.255.0 | 192.168.11.1 |
| PC3 | NIC | 10.1.1.10 | 255.255.255.0 | 10.1.1.1 |
| PC4 | NIC | 10.1.2.10 | 255.255.255.0 | 10.1.2.1 |

## Objectives

- **Part 1:** Display Router Information
- **Part 2:** Configure Router Interfaces
- **Part 3:** Verify the Configuration

## Background

In this activity, you will use various `show` commands to display the current state of the router. You will then use the Addressing Table to configure router Ethernet interfaces. Finally, you will use commands to verify and test your configurations.

> **Note:** The routers in this activity are partially configured. Some of the configurations are not covered in this course but they are provided to assist you in using verification commands.

---

## Part 1: Display Router Information

> Click a device and then click the **CLI** tab to access the command line directly. The console password is `cisco`. The privileged EXEC password is `class`.

### Step 1: Display interface information on R1.

a. Which command displays the statistics for all interfaces configured on a router?
```
R1# show interfaces
```

b. Which command displays the information about the Serial 0/0/0 interface only?
```
R1# show interfaces serial 0/0/0
```

c. Enter the command to display the statistics for the Serial 0/0/0 interface on R1:
```
R1# show interfaces s0/0/0
```
> **Questions:**
> - What is the IP address configured on R1? → `209.165.200.225` (from the Addressing Table — confirm against your actual output)
> - What is the bandwidth on the Serial 0/0/0 interface? → *(1544 Kbit)*

d. Enter the command to display the statistics for the GigabitEthernet 0/0 interface:
```
R1# show interfaces gigabitEthernet 0/0
```
> **Questions:**
> - What is the IP address on R1? → *(this interface is not configured yet at this point in the lab — check output; it should show "no IP address" until Part 2)*
> - What is the MAC address of the GigabitEthernet 0/0 interface? → *(000d.bd6c.7d01)*
> - What is the bandwidth (BW) of the GigabitEthernet 0/0 interface? → *(100,000 Kbit)*

### Step 2: Display a summary list of the interfaces on R1.

a. Which command displays a brief summary of the current interfaces, interface status, and IP addresses assigned to them?
```
R1# show ip interface brief
```

b. Enter the command on each router:
```
R1# show ip interface brief
R2# show ip interface brief
```
> **Questions:**
> - How many serial interfaces are there on R1 and R2? → **2 each** (S0/0/0 and S0/0/1)
> - How many Ethernet interfaces are there on R1 and R2? → **6 each** (G0/0-1 and F0/1/0-3)
> - Are all the Ethernet interfaces on R1 the same? If no, explain the difference(s). → * 4 are Fast Ethernet providing speeds up to 100Mbps, while the other 2 Gigabit Ethernet deliver data up to 1,000 Mbps *

### Step 3: Display the routing table on R1.

a. What command displays the contents of the routing table?
```
R1# show ip route
```

b. Enter the command on R1:
```
R1# show ip route
```
> **Questions:**
> - How many connected routes are there (uses the `C` code)? → 1
> - Which route is listed? → Serial0/0/0 link `209.165.200.224/30`
> - How does a router handle a packet destined for a network that is not listed in the routing table? → **The router drops the packet** since it has no route to forward it on.

---

## Part 2: Configure Router Interfaces

### Step 1: Configure the GigabitEthernet 0/0 interface on R1.

```
R1(config)# interface gigabitethernet 0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
```
```
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
```

Configure a description documenting what this interface connects to:
```
R1(config-if)# description LAN connection to S1
```

Return to privileged EXEC mode and test:
```
R1(config-if)# end
%SYS-5-CONFIG_I: Configured from console by console
R1# ping 192.168.10.10
```

### Step 2: Configure the remaining Gigabit Ethernet interfaces on R1 and R2.

Use the Addressing Table above to finish the interface configurations.

**R1 G0/1:**
```
R1(config)# interface gigabitethernet 0/1
R1(config-if)# ip address 192.168.11.1 255.255.255.0
R1(config-if)# description LAN connection to S2
R1(config-if)# no shutdown
R1(config-if)# exit
```

**R2 G0/0:**
```
R2(config)# interface gigabitethernet 0/0
R2(config-if)# ip address 10.1.1.1 255.255.255.0
R2(config-if)# description LAN connection to S3
R2(config-if)# no shutdown
R2(config-if)# exit
```

**R2 G0/1:**
```
R2(config)# interface gigabitethernet 0/1
R2(config-if)# ip address 10.1.2.1 255.255.255.0
R2(config-if)# description LAN connection to S4
R2(config-if)# no shutdown
R2(config-if)# exit
```

Verify each interface came up:
```
R1# show ip interface brief
R2# show ip interface brief
```

### Step 3: Back up the configurations to NVRAM.

```
R1# copy run start
```

> **Question:** What command did you use? → `copy running-config startup-config` (can be abbreviated `copy run start`)

---

## Part 3: Verify the Configuration

### Step 1: Use verification commands to check your interface configurations.

a. Check IP addresses and status on both routers:
```
R1# show ip interface brief
R2# show ip interface brief
```

> **Questions:**
> - How many interfaces on R1 and R2 are configured with IP addresses and in the "up" and "up" state? → **3 each** (both Gigabit interfaces + the Serial interface, once all are configured/activated)
> - What part of the interface configuration is NOT displayed in the command output? → The **interface description** is not shown in `show ip interface brief`.
> - What commands can you use to verify this part of the configuration? →
>   ```
>   show interfaces
>   ```
>   or
>   ```
>   show running-config interface g0/0
>   ```

b. Use `show ip route` on both routers to view the current routing tables:
```
R1# show ip route
R2# show ip route
```

<img width="706" height="678" alt="lab8 1" src="https://github.com/user-attachments/assets/1c6b7aae-2f02-4224-9a2e-a35fd573ec60" />
<img width="682" height="628" alt="lab8 2" src="https://github.com/user-attachments/assets/449c53b0-6f75-442a-a0ce-a1249da4551b" />



> **Questions:**
> 1. How many connected routes (`C` code) do you see on each router? → **3 each** (2 LAN interfaces + 1 WAN/serial interface, once fully configured)
> 2. How many OSPF routes (`O` code) do you see on each router? → 2
> 3. If the router knows all the routes in the network, the number of connected + dynamically learned (OSPF) routes should equal the total number of LANs and WANs. How many LANs and WANs are in the topology? → **5 total**:
> 4. Does this number match the number of `C` and `O` routes shown in the routing table? → YES1

### Step 2: Test end-to-end connectivity across the network.

You should now be able to ping from any PC to any other PC on the network, and to the active router interfaces.

```
PC1> ping 10.1.2.10
```
(PC1 → PC4)

```
R2# ping 192.168.11.10
```
(R2 → PC2)

## Part 4: Result of Assessment

<img width="1377" height="504" alt="lab8 3" src="https://github.com/user-attachments/assets/51957dc1-2ea4-4a9b-8693-92e66a3eda78" />
