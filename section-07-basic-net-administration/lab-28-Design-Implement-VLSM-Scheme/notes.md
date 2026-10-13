
# Packet Tracer - Design and Implement a VLSM Addressing Scheme

## Objectives

- Design a VLSM IP addressing scheme given requirements.
- Configure addressing on network devices and hosts.
- Verify IP connectivity.
- Troubleshoot connectivity issues as required.

## Background / Scenario

You have been asked to design, implement, and test an addressing scheme for a customer. The customer gave you the network address `172.19.67.0/24`, the topology, and the host requirements.
---

## The Host Counts Don't Match

The topology labels in your Packet Tracer file and the Requirements table in the instructions **disagree**:

| LAN | Hosts per **topology labels** | Hosts per **instructions table** |
|---|---|---|
| PD-1 | 23 | 19 |
| PD-2 | 19 | 23 |
| PS-101 | 7 | 11 |
| PS-115 | 11 | 7 |

The subnet **sizes** are identical either way (23 and 19 both need a /27; 11 and 7 both need a /28). The only thing that changes is **which LAN gets which block**.

---

## Part 1: Design the VLSM Scheme

### The method (largest subnet first)

VLSM works by giving each LAN **only as many addresses as it needs**, and allocating **largest to smallest** so the blocks stay contiguous with no gaps.

For each LAN, find the smallest block where `2^(host bits) − 2 ≥ hosts needed`:

| LAN | Hosts needed | Hosts + router + switch | Smallest fit | Usable hosts | Block size |
|---|---|---|---|---|---|
| PD-1 | 23 | 25 | /27 (5 host bits) | 30 | 32 |
| PD-2 | 19 | 21 | /27 | 30 | 32 |
| PS-115 | 11 | 13 | /28 (4 host bits) | 14 | 16 |
| PS-101 | 7 | 9 | /28 | 14 | 16 |
| WAN (Police ↔ Schools) | 2 | 2 | /30 (2 host bits) | 2 | 4 |

Notes on why these fit:
- A /29 gives only 6 usable hosts, which is not enough for 7, so PS-101 needs a /28.
- The "+ router + switch" column confirms each LAN also has room for its router interface and switch management address. PS-115 is the tight one (13 of 14).
- The WAN link needs exactly 2 addresses (one per router), so **/30** is the most efficient choice, as the lab requires.

**Masks:** /27 = `255.255.255.224`, /28 = `255.255.255.240`, /30 = `255.255.255.252`.

### Subnet design table

Start at `172.19.67.0` and add each block size to find the next subnet:

| Subnet Description | Hosts Needed | Network Address/CIDR | First Usable Host | Broadcast Address |
|---|---|---|---|---|
| PD-1 LAN | 23 | 172.19.67.0/27 | 172.19.67.1 | 172.19.67.31 |
| PD-2 LAN | 19 | 172.19.67.32/27 | 172.19.67.33 | 172.19.67.63 |
| PS-115 LAN | 11 | 172.19.67.64/28 | 172.19.67.65 | 172.19.67.79 |
| PS-101 LAN | 7 | 172.19.67.80/28 | 172.19.67.81 | 172.19.67.95 |
| WAN (Police ↔ Schools) | 2 | 172.19.67.96/30 | 172.19.67.97 | 172.19.67.99 |

**Contiguity check:** 0–31, 32–63, 64–79, 80–95, 96–99. No gaps between subnets. Everything from `172.19.67.100` onward is unused and available for growth.

### Alternate mapping (if the instructions table is right)

If the instructions table (PD-1 = 19, PD-2 = 23, PS-101 = 11, PS-115 = 7) is the correct one, the numbers above stay the same but the LAN names swap:

| Block | Network/CIDR | Topology-label version | Instructions-table version |
|---|---|---|---|
| 1st /27 | 172.19.67.0/27 | PD-1 | **PD-2** |
| 2nd /27 | 172.19.67.32/27 | PD-2 | **PD-1** |
| 1st /28 | 172.19.67.64/28 | PS-115 | **PS-101** |
| 2nd /28 | 172.19.67.80/28 | PS-101 | **PS-115** |
| WAN /30 | 172.19.67.96/30 | same | same |

---

## Part 2: Fill In the Addressing Table

**Rules from the lab:**
- **Police:** first usable address on both LAN links and the WAN link.
- **Schools:** first usable on both LAN links, **last** usable on the WAN link.
- **Switches:** second usable address in their subnet.
- **Hosts:** last usable address in their subnet.

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| Police | G0/0 | 172.19.67.1 *(PD-1 LAN)* | 255.255.255.224 | N/A |
| Police | G0/1 | 172.19.67.33 *(PD-2 LAN)* | 255.255.255.224 | N/A |
| Police | S0/0/0 | 172.19.67.97 | 255.255.255.252 | N/A |
| Schools | G0/0 | 172.19.67.65 *(PS-115 LAN)* | 255.255.255.240 | N/A |
| Schools | G0/1 | 172.19.67.81 *(PS-101 LAN)* | 255.255.255.240 | N/A |
| Schools | S0/0/0 | 172.19.67.98 | 255.255.255.252 | N/A |
| PD-1 | VLAN 1 | 172.19.67.2 | 255.255.255.224 | 172.19.67.1 |
| PD-2 | VLAN 1 | 172.19.67.34 | 255.255.255.224 | 172.19.67.33 |
| PS-101 | VLAN 1 | 172.19.67.82 | 255.255.255.240 | 172.19.67.81 |
| PS-115 | VLAN 1 | 172.19.67.66 | 255.255.255.240 | 172.19.67.65 |
| PD-1-11 | NIC | 172.19.67.30 | 255.255.255.224 | 172.19.67.1 |
| PD-2-23 | NIC | 172.19.67.62 | 255.255.255.224 | 172.19.67.33 |
| PS-101-87 | NIC | 172.19.67.94 | 255.255.255.240 | 172.19.67.81 |
| PS-115-12 | NIC | 172.19.67.78 | 255.255.255.240 | 172.19.67.65 |

---

## Part 3: Configure the Devices

### Step 1: Configure Police

```
Router> enable
Router# configure terminal
Router(config)# hostname Police

Police(config)# interface g0/0
Police(config-if)# description LAN to PD-1
Police(config-if)# ip address 172.19.67.1 255.255.255.224
Police(config-if)# no shutdown
Police(config-if)# exit

Police(config)# interface g0/1
Police(config-if)# description LAN to PD-2
Police(config-if)# ip address 172.19.67.33 255.255.255.224
Police(config-if)# no shutdown
Police(config-if)# exit

Police(config)# interface s0/0/0
Police(config-if)# description WAN to Schools
Police(config-if)# ip address 172.19.67.97 255.255.255.252
Police(config-if)# no shutdown
Police(config-if)# end

Police# copy running-config startup-config
```

### Step 2: Configure Schools

```
Router> enable
Router# configure terminal
Router(config)# hostname Schools

Schools(config)# interface g0/0
Schools(config-if)# description LAN to PS-115
Schools(config-if)# ip address 172.19.67.65 255.255.255.240
Schools(config-if)# no shutdown
Schools(config-if)# exit

Schools(config)# interface g0/1
Schools(config-if)# description LAN to PS-101
Schools(config-if)# ip address 172.19.67.81 255.255.255.240
Schools(config-if)# no shutdown
Schools(config-if)# exit

Schools(config)# interface s0/0/0
Schools(config-if)# description WAN to Police
Schools(config-if)# ip address 172.19.67.98 255.255.255.252
Schools(config-if)# no shutdown
Schools(config-if)# end

Schools# copy running-config startup-config
```

### Step 3: Configure the four switches

**PD-1**
```
Switch> enable
Switch# configure terminal
PD-1(config)# interface vlan 1
PD-1(config-if)# ip address 172.19.67.2 255.255.255.224
PD-1(config-if)# no shutdown
PD-1(config-if)# exit
PD-1(config)# ip default-gateway 172.19.67.1
PD-1(config)# end
PD-1# copy running-config startup-config
```

**PD-2**
```
PD-2(config)# interface vlan 1
PD-2(config-if)# ip address 172.19.67.34 255.255.255.224
PD-2(config-if)# no shutdown
PD-2(config-if)# exit
PD-2(config)# ip default-gateway 172.19.67.33
PD-2(config)# end
PD-2# copy running-config startup-config
```

**PS-115**
```
PS-115(config)# interface vlan 1
PS-115(config-if)# ip address 172.19.67.66 255.255.255.240
PS-115(config-if)# no shutdown
PS-115(config-if)# exit
PS-115(config)# ip default-gateway 172.19.67.65
PS-115(config)# end
PS-115# copy running-config startup-config
```

**PS-101**
```
PS-101(config)# interface vlan 1
PS-101(config-if)# ip address 172.19.67.82 255.255.255.240
PS-101(config-if)# no shutdown
PS-101(config-if)# exit
PS-101(config)# ip default-gateway 172.19.67.81
PS-101(config)# end
PS-101# copy running-config startup-config
```

### Step 4: Configure the four PCs

Click each PC → **Desktop → IP Configuration** (static):

| PC | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PD-1-11 | 172.19.67.30 | 255.255.255.224 | 172.19.67.1 |
| PD-2-23 | 172.19.67.62 | 255.255.255.224 | 172.19.67.33 |
| PS-115-12 | 172.19.67.78 | 255.255.255.240 | 172.19.67.65 |
| PS-101-87 | 172.19.67.94 | 255.255.255.240 | 172.19.67.81 |

---