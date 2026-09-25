
# Packet Tracer - Basic Switch and End Device Configuration

## Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| Class-A | VLAN 1 | 10.10.10.100 | 255.255.255.0 |
| Class-B | VLAN 1 | 10.10.10.150 | 255.255.255.0 |
| Student-1 | NIC | 10.10.10.4 | 255.255.255.0 |
| Student-2 | NIC | 10.10.10.5 | 255.255.255.0 |

## Objectives

- Configure hostnames and IP addresses on two Cisco Internetwork Operating System (IOS) switches using the command-line interface (CLI).
- Use Cisco IOS commands to specify or limit access to the device configurations.
- Use IOS commands to save the running configuration.
- Configure two host devices with IP addresses.
- Verify connectivity between the two PC end devices.

## Scenario

As a recently hired LAN technician, your network manager has asked you to demonstrate your ability to configure a small LAN. Your tasks include configuring initial settings on two switches using the Cisco IOS and configuring IP address parameters on host devices to provide end-to-end connectivity. You are to use two switches and two hosts/PCs on a cabled and powered network.

## Instructions

Configure the devices to fulfill the requirements below.

## Requirements

- Use a console connection to access each switch.
- Name the `Class-A` and `Class-B` switches.
- Use the `8ubRu` password for all lines.
- Use the `C9WrE` secret password.
- Encrypt all clear text passwords.
- Configure an appropriate message-of-the-day (MOTD) banner.
- Configure addressing for all devices according to the Addressing Table.
- Save your configurations.
- Verify connectivity between all devices.

> **Note:** Click **Check Results** to see your progress. Click **Reset Activity** to generate a new set of requirements. If you click this before you complete the activity, all configurations will be lost.

**ID:** 0122

---

## Steps to Complete

### 1. Configure Class-A switch

Click **Class-A** → **CLI** tab, then:

```
Switch> enable
Switch# configure terminal
Switch(config)# hostname Class-A
```

Secure the console line:
```
Class-A(config)# line console 0
Class-A(config-line)# password 8ubRu
Class-A(config-line)# login
Class-A(config-line)# exit
```

Secure the VTY lines (remote access):
```
Class-A(config)# line vty 0 15
Class-A(config-line)# password 8ubRu
Class-A(config-line)# login
Class-A(config-line)# exit
```

Set the enable secret password:
```
Class-A(config)# enable secret C9WrE
```

Encrypt all plaintext passwords:
```
Class-A(config)# service password-encryption
```

Configure a MOTD banner:
```
Class-A(config)# banner motd "Authorized access only. All activity is monitored."
```

Configure the VLAN 1 management IP address (from the Addressing Table):
```
Class-A(config)# interface vlan 1
Class-A(config-if)# ip address 10.10.10.100 255.255.255.0
Class-A(config-if)# no shutdown
Class-A(config-if)# exit
```

Save the configuration:
```
Class-A(config)# exit
Class-A# copy running-config startup-config
```

### 2. Configure Class-B switch

Click **Class-B** → **CLI** tab, then repeat the same steps with the Class-B hostname and its own address:

```
Switch> enable
Switch# configure terminal
Switch(config)# hostname Class-B

Class-B(config)# line console 0
Class-B(config-line)# password 8ubRu
Class-B(config-line)# login
Class-B(config-line)# exit

Class-B(config)# line vty 0 15
Class-B(config-line)# password 8ubRu
Class-B(config-line)# login
Class-B(config-line)# exit

Class-B(config)# enable secret C9WrE
Class-B(config)# service password-encryption
Class-B(config)# banner motd "Authorized access only. All activity is monitored."

Class-B(config)# interface vlan 1
Class-B(config-if)# ip address 10.10.10.150 255.255.255.0
Class-B(config-if)# no shutdown
Class-B(config-if)# exit

Class-B(config)# exit
Class-B# copy running-config startup-config
```

### 3. Configure Student-1 (PC)

Click **Student-1** → **Desktop** tab → **IP Configuration**:

- IP Address: `10.10.10.4`
- Subnet Mask: `255.255.255.0`
- (No gateway is needed — both PCs and switches are on the same VLAN/subnet, so there's no router in this topology.)

### 4. Configure Student-2 (PC)

Click **Student-2** → **Desktop** tab → **IP Configuration**:

- IP Address: `10.10.10.5`
- Subnet Mask: `255.255.255.0`

### 5. Verify connectivity

- From **Student-1**'s Desktop → **Command Prompt**, ping the other devices:
  ```
  ping 10.10.10.5
  ping 10.10.10.100
  ping 10.10.10.150
  ```
- From **Student-2**, ping the same set of devices back.
- On each switch, confirm the config landed correctly:
  ```
  Class-A# show running-config
  ```
  Check that: hostname is set, passwords are encrypted (not plaintext), MOTD banner appears, VLAN 1 has the correct IP.

### 6. Troubleshoot (if a ping fails)

- [ ] Confirm the VLAN 1 interface is `no shutdown` on both switches
- [ ] Confirm the PC's IP/subnet mask matches the `10.10.10.0/24` range
- [ ] Confirm all devices are actually cabled and the link lights are up in the topology view
- [ ] Re-check `show running-config` on both switches for typos in the IP addresses

### 7. Final check

- [ ] Both switches respond with the MOTD banner and prompt for the console password (`8ubRu`) before reaching user EXEC
- [ ] Entering `enable` prompts for the secret password (`C9WrE`)
- [ ] `show running-config` shows all passwords encrypted (numbers other than `0` next to `password`, e.g. `password 7 <encrypted string>`)
- [ ] Both PCs can ping each other and both switches successfully
- [ ] Configuration saved on both switches (`copy running-config startup-config` completed with `[OK]`)
- [ ] Click **Check Results** in Packet Tracer to confirm 100%

---