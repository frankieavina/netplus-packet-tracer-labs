
# Packet Tracer – Subnet an IPv4 Network

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| CustomerRouter | G0/0 | 192.168.0.1 | 255.255.255.192| N/A |
| CustomerRouter | G0/1 | 192.168.0.65 | 255.255.255.192| N/A |
| CustomerRouter | S0/1/0 | 209.165.201.2 | 255.255.255.252 | N/A |
| LAN-A Switch | VLAN1 | 192.168.0.2 | 255.255.255.192| 192.168.0.1 |
| LAN-B Switch | VLAN1 | 192.168.0.66 | 255.255.255.192| 192.168.0.65 |
| PC-A | NIC | 192.168.0.62 | 255.255.255.192| 192.168.0.1 |
| PC-B | NIC | 192.168.0.126 | 255.255.255.192| 192.168.0.65 |
| ISPRouter | G0/0 | 209.165.200.225 | 255.255.255.224 | N/A |
| ISPRouter | S0/1/0 | 209.165.201.1 | 255.255.255.252 | N/A |
| ISPSwitch | VLAN1 | 209.165.200.226 | 255.255.255.224 | 209.165.200.225 |
| ISP Workstation | NIC | 209.165.200.235 | 255.255.255.224 | 209.165.200.225 |
| ISP Server | NIC | 209.165.200.240 | 255.255.255.224 | 209.165.200.225 |

## Objectives

- **Part 1:** Design an IPv4 Network Subnetting Scheme
- **Part 2:** Configure the Devices
- **Part 3:** Test and Troubleshoot the Network

## Background / Scenario

In this activity, you will subnet the Customer network into multiple subnets. The subnet scheme should be based on the number of host computers required in each subnet, as well as other network considerations, like future network host expansion.

After you have created a subnetting scheme and completed the table by filling in the missing host and interface IP addresses, you will configure the host PCs, switches and router interfaces.

After the network devices and host PCs have been configured, you will use the `ping` command to test for network connectivity.

---

## Part 1: Subnet the Assigned Network

### Step 1: Create a subnetting scheme that meets the required number of subnets and required number of host addresses.

You are a network technician assigned to install a new network for a customer. You must create multiple subnets out of the `192.168.0.0/24` network address space to meet the following requirements:

a. The first subnet is the **LAN-A** network. You need a minimum of **50** host IP addresses.

b. The second subnet is the **LAN-B** network. You need a minimum of **40** host IP addresses.

c. You also need at least **two additional unused subnets** for future network expansion.

> **Note:** Variable length subnet masks will not be used. All of the device subnet masks should be the same length.

d. Answer the following questions to help create a subnetting scheme that meets the stated network requirements:

> **Questions:**
> - How many host addresses are needed in the largest required subnet? 50
> - What is the minimum number of subnets required? 4
> - The network that you are tasked to subnet is `192.168.0.0/24`. What is the /24 subnet mask in binary? 11111111 11111111 11111111 000000000

e. The subnet mask is made up of two portions, the network portion, and the host portion. This is represented in the binary by the ones and the zeros in the subnet mask.

> **Questions:**
> - In the network mask, what do the ones represent? The network id, shows specific network a device belongs to
> - In the network mask, what do the zeros represent? The host id, providing space needed to identify individual devices on network

f. To subnet a network, bits from the host portion of the original network mask are changed into subnet bits. The number of subnet bits defines the number of subnets.

> **Questions:** Given each of the possible subnet masks depicted in the following binary format, how many subnets and how many hosts are created in each example?

| # | Prefix | Binary Mask | Dotted Decimal Equivalent | Number of Subnets | Number of Hosts |
|---|---|---|---|---|---|
| 1 | /25 | 11111111.11111111.11111111.**1**0000000 | | 2| 126|
| 2 | /26 | 11111111.11111111.11111111.**11**000000 | | 4| 62|
| 3 | /27 | 11111111.11111111.11111111.**111**00000 | | 8|30 |
| 4 | /28 | 11111111.11111111.11111111.**1111**0000 | | 16| 14|
| 5 | /29 | 11111111.11111111.11111111.**11111**000 | | 32| 6|
| 6 | /30 | 11111111.11111111.11111111.**111111**00 | |64 | 2|

> **Questions:**
> - Considering your answers above, which subnet masks meet the required minimum number of host addresses?/25 and /26
> - Which subnet masks meet the minimum number of subnets required?/26 and smaller
> - Which subnet mask meets **both** the required minimum number of hosts and the minimum number of subnets required?/26 (255.255.255.192)

When you have determined which subnet mask meets all of the stated network requirements, derive each of the subnets. List the subnets from first to last (the first subnet is `192.168.0.0` with the chosen subnet mask).

| Subnet Address | Prefix | Subnet Mask |
|---|---|---|
| 192.168.0.0 | /26 | 255.255.255.192|
| 192.168.0.64 | /26 | 255.255.255.192|
| 192.168.0.128 | /26 | 255.255.255.192|
| 192.168.0.192 | /26 | 255.255.255.192|

### Step 2: Fill in the missing IP addresses in the Addressing Table.

Assign IP addresses based on the following criteria (use the ISP Network settings in the table above as an example):

a. Assign the **first subnet** to LAN-A.
   1. Use the **first host address** for the CustomerRouter interface connected to the LAN-A switch.
   2. Use the **second host address** for the LAN-A switch. Assign a default gateway address for the switch.
   3. Use the **last host address** for PC-A. Assign a default gateway address for the PC.

b. Assign the **second subnet** to LAN-B.
   1. Use the **first host address** for the CustomerRouter interface connected to the LAN-B switch.
   2. Use the **second host address** for the LAN-B switch. Assign a default gateway address for the switch.
   3. Use the **last host address** for PC-B. Assign a default gateway address for the PC.

---

## Part 2: Configure the Devices

Configure basic settings on the PCs, switches, and router, using the addresses you derived in Part 1.

### Step 1: Configure CustomerRouter.

```
Router> enable
Router# configure terminal
```

a. Set the enable secret password:
```
Router(config)# enable secret Class123
```

b. Set the console login password:
```
Router(config)# line console 0
Router(config-line)# password Cisco123
Router(config-line)# login
Router(config-line)# exit
```

c. Configure the hostname:
```
Router(config)# hostname CustomerRouter
```

d. Configure G0/0 and G0/1 with the IP addresses/subnet masks you derived in Part 1, and enable them (replace the `<...>` placeholders with your actual addresses):

```
CustomerRouter(config)# interface gigabitethernet 0/0
CustomerRouter(config-if)# ip address <LAN-A first host address> <subnet mask>
CustomerRouter(config-if)# no shutdown
CustomerRouter(config-if)# exit

CustomerRouter(config)# interface gigabitethernet 0/1
CustomerRouter(config-if)# ip address <LAN-B first host address> <subnet mask>
CustomerRouter(config-if)# no shutdown
CustomerRouter(config-if)# exit
```

e. Save the running configuration:
```
CustomerRouter(config)# exit
CustomerRouter# copy running-config startup-config
```

### Step 2: Configure the two customer LAN switches.

Configure the IP address on interface VLAN 1 on each switch, plus the correct default gateway.

**LAN-A Switch:**
```
Switch> enable
Switch# configure terminal
Switch(config)# hostname LAN-A-Switch
LAN-A-Switch(config)# interface vlan 1
LAN-A-Switch(config-if)# ip address <LAN-A second host address> <subnet mask>
LAN-A-Switch(config-if)# no shutdown
LAN-A-Switch(config-if)# exit
LAN-A-Switch(config)# ip default-gateway <LAN-A first host address>
LAN-A-Switch(config)# exit
LAN-A-Switch# copy running-config startup-config
```

**LAN-B Switch:**
```
Switch> enable
Switch# configure terminal
Switch(config)# hostname LAN-B-Switch
LAN-B-Switch(config)# interface vlan 1
LAN-B-Switch(config-if)# ip address <LAN-B second host address> <subnet mask>
LAN-B-Switch(config-if)# no shutdown
LAN-B-Switch(config-if)# exit
LAN-B-Switch(config)# ip default-gateway <LAN-B first host address>
LAN-B-Switch(config)# exit
LAN-B-Switch# copy running-config startup-config
```

### Step 3: Configure the PC interfaces.

**PC-A** (Desktop → IP Configuration):
- IP Address: `<LAN-A last host address>`
- Subnet Mask: `<subnet mask>`
- Default Gateway: `<LAN-A first host address>`

**PC-B** (Desktop → IP Configuration):
- IP Address: `<LAN-B last host address>`
- Subnet Mask: `<subnet mask>`
- Default Gateway: `<LAN-B first host address>`

---

## Part 3: Test and Troubleshoot the Network

Use the `ping` command to test network connectivity.

a. Determine if PC-A can communicate with its default gateway:
```
ping <LAN-A first host address>
```
> Do you get a reply?

b. Determine if PC-B can communicate with its default gateway:
```
ping <LAN-B first host address>
```
> Do you get a reply?

c. Determine if PC-A can communicate with PC-B:
```
ping <LAN-B last host address>
```
> Do you get a reply?

<img width="742" height="816" alt="Screenshot 2026-10-01 at 5 06 15 PM" src="https://github.com/user-attachments/assets/da0ef669-ae6d-47b2-b150-5d023e4df145" />
<img width="1289" height="830" alt="Screenshot 2026-10-01 at 5 06 39 PM" src="https://github.com/user-attachments/assets/5a514a88-264b-4e46-9de4-9cea1ac17719" />
