
# Packet Tracer - Build a Switch and Router Network

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| R1 | G0/0/0 | 192.168.0.1 | 255.255.255.0 | N/A |
| R1 | G0/0/1 | 192.168.1.1 | 255.255.255.0 | N/A |
| S1 | VLAN 1 | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| PC-A | NIC | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |
| PC-B | NIC | 192.168.0.3 | 255.255.255.0 | 192.168.0.1 |

## Objectives

**Part 1: Configure Devices and Verify Connectivity**
- Assign static IP information to the PC interfaces.
- Configure the router and switch.
- Verify network connectivity.

**Part 2: Display Device Information**
- Retrieve hardware and software information from the network devices.
- Interpret the output from the routing table.
- Display interface information on the router.
- Display a summary list of the interfaces on the router and switch.

**Part 3: Secure Remote Access to the Router**
- Set the IP domain name and generate secure keys.
- Create an SSH user and configure VTY lines for SSH-only access.
- Verify SSH Implementation.

## Background / Scenario

In this lab, you will cable the equipment and configure the devices to match the Addressing Table. After the configurations have been saved, you will verify your configurations by testing for network connectivity.

After the devices have been configured and network connectivity has been verified, you will use IOS commands to retrieve information from the devices to answer questions about your network equipment. You will also access the router remotely via SSH.

---

## Instructions & Steps to Complete

### Part 1: Configure Devices and Verify Connectivity

#### Step 1: Connect the devices.

The devices are already deployed in the workspace. Connect them using the correct cables:

- [ ] Connect **PCA F0** to **S1 F0/1**.
- [ ] Connect **S1 G0/1** to **R1 G0/0/1**.
- [ ] Connect **R1 G0/0/0** to **PCB F0**.

#### Step 2: Assign static IP information to the PC interfaces.

a. Configure **PC-A** (Desktop → IP Configuration):
- IP Address: `192.168.1.3`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.1.1`

b. Configure **PC-B** (Desktop → IP Configuration):
- IP Address: `192.168.0.3`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.0.1`

c. Ping PC-B from a command prompt window on PC-A:
```
ping 192.168.0.3
```

> **Question:** Why were the pings not successful?
> *(Answer: R1 hasn't been configured yet — its interfaces are down/unconfigured, so there's no path between the two subnets.)*

#### Step 3: Configure R1.

a. Console into the router and enable privileged EXEC mode:
```
Router> enable
Router#
```

b. Enter configuration mode:
```
Router# configure terminal
```

c. Assign the device name:
```
Router(config)# hostname R1
```

d. Assign `class` as the privileged EXEC encrypted password:
```
R1(config)# enable secret class
```

e. Assign `cisco` as the console password and enable login:
```
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
```

f. Encrypt the plaintext passwords:
```
R1(config)# service password-encryption
```

g. Configure a banner warning against unauthorized access:
```
R1(config)# banner motd "Unauthorized access is strictly prohibited."
```

h. Configure the IP addresses and activate both Ethernet interfaces:
```
R1(config)# interface g0/0/0
R1(config-if)# ip address 192.168.0.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface g0/0/1
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

i. Save the running configuration:
```
R1(config)# exit
R1# copy running-config startup-config
```

> **Question:** Were the pings successful? Explain.
> *(Answer: Yes — once R1's interfaces are configured and up, R1 can route between the 192.168.0.0/24 and 192.168.1.0/24 subnets, so PC-A and PC-B can now reach each other through R1.)*

#### Step 4: Configure S1.

> **Note:** Most of the commands on the switch are similar to the commands on the router. Use the help (`?`) context as necessary.

a. Console into the switch and enable privileged EXEC mode:
```
Switch> enable
Switch#
```

b. Enter configuration mode:
```
Switch# configure terminal
```

c. Assign the device name:
```
Switch(config)# hostname S1
```

d. Assign `class` as the privileged EXEC encrypted password:
```
S1(config)# enable secret class
```

e. Assign `cisco` as the console password and enable login:
```
S1(config)# line console 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# exit
```

f. Encrypt the plaintext passwords:
```
S1(config)# service password-encryption
```

g. Configure a banner warning against unauthorized access:
```
S1(config)# banner motd "Unauthorized access is strictly prohibited."
```

h. Configure the IP address for the VLAN 1 SVI and activate the interface:
```
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.2 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
```

i. Configure the default gateway:
```
S1(config)# ip default-gateway 192.168.1.1
```

j. Save the running configuration:
```
S1(config)# exit
S1# copy running-config startup-config
```

### Part 2: Display Device Information

#### Step 1: Retrieve hardware and software information from the network devices.

a. On the router:
```
R1# show version
```
> **Question:** What is the name of the IOS image that the router is running?
> *(Check the output — look for the line showing the `.bin` image filename, e.g. something like `c2900-universalk9-mz.SPA.xxx.bin`.)*

b. On the switch:
```
S1# show version
```
> **Questions:**
> - What IOS software image and version is running on the switch?
> - What is the model number of the switch? *(e.g., Catalyst 2960)*

#### Step 2: Display the routing table on the router.

```
R1# show ip route
```

> **Questions:**
> - What code is used in the routing table to indicate a directly connected network? → **`C`**
> - How many route entries are coded with a `C` code in the routing table? → *(one per directly connected/active interface — check your output; with both G0/0/0 and G0/0/1 up, expect 2)*
> - What interface types are associated with the `C` coded routes? → **GigabitEthernet** (G0/0/0 and G0/0/1)

#### Step 3: Display interface information on the router.

```
R1# show interfaces g0/0/1
```

> **Questions:**
> - What is the operational status of the G0/0/1 interface? → should read **up, line protocol is up** if correctly configured
> - What is the MAC address of the G0/0/1 interface? → *(read from the output, listed as "Hardware is ..., address is ...")*
> - How is the Internet address displayed in this command? → shown with its **prefix length** (e.g., `192.168.1.1/24`) rather than a separate subnet mask

#### Step 4: Display a summary list of the interfaces on the router and switch.

a. On the router:
```
R1# show ip interface brief
```

b. On the switch:
```
S1# show ip interface brief
```

Confirm G0/0/0, G0/0/1 (router) and VLAN 1 (switch) all show `up/up` with the correct IP addresses.

### Part 3: Secure Remote Access to the Router

#### Step 1: Set the IP domain name and generate secure keys.

a. Configure the domain name:
```
R1(config)# ip domain-name academy.net
```

b. Generate RSA keys with a 1024-bit modulus:
```
R1(config)# crypto key generate rsa
```
When prompted for the key modulus size, enter:
```
1024
```

#### Step 2: Create an SSH user and configure VTY lines for SSH-only access.

a. Create the SSH user:
```
R1(config)# username SSHuser secret cisco
```

b. Configure the VTY lines to authenticate against the local username database:
```
R1(config)# line vty 0 4
R1(config-line)# login local
```

c. Restrict VTY access to SSH only (disable Telnet):
```
R1(config-line)# transport input ssh
R1(config-line)# exit
```

#### Step 3: Verify SSH Implementation.

a. Click **PCA** → **Desktop** tab → **Command Prompt**.

b. At the prompt, enter:
```
ssh -l SSHuser 192.168.1.1
```

c. Enter `cisco` when prompted for the password.

> **Question:** What is the displayed message?
> *(You should land at the R1 privileged/user EXEC prompt over SSH, e.g. `R1>` or similar — confirming the SSH session succeeded.)*

If unsuccessful, double check: RSA keys were generated, `login local` and `transport input ssh` are both set on VTY 0 4, and the username/password match exactly.

---

## Reflection

1. If the G0/0/1 interface showed administratively down, what interface configuration command would you use to turn the interface up?
   ```
   R1(config-if)# no shutdown
   ```

2. What would happen if you had incorrectly configured interface G0/0/1 on the router with an IP address of `192.168.1.2`?
   *(That's the same address already assigned to S1's VLAN 1 interface — this would create an IP address conflict on the 192.168.1.0/24 subnet, causing unpredictable connectivity issues since two devices would claim the same IP.)*

---