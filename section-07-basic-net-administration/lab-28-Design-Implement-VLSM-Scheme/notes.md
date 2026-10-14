# Packet Tracer - Configure Secure Passwords and SSH

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| RTA | G0/0 | 172.16.1.1 | 255.255.255.0 | N/A |
| PCA | NIC | 172.16.1.10 | 255.255.255.0 | 172.16.1.1 |
| SW1 | VLAN 1 | 172.16.1.2 | 255.255.255.0 | 172.16.1.1 |

## Scenario

The network administrator has asked you to prepare RTA and SW1 for deployment. Before they can be connected to the network, security measures must be enabled.

> **Before you start:** Pick your own secret password and username, and **write them down**. If you lock yourself out, you'll have to reset the activity. Examples used below are placeholders; use your own:
>
> | Item | Example (replace with yours) |
> |---|---|
> | Enable secret | `Str0ng!Secret26` |
> | SSH username | `admin01` |
> | SSH user password | `Adm1n!Passw0rd` |
>
> Every password must be **at least 10 characters**, because you'll set `security password min-length 10` on the router.

---

## Step 1: Configure Basic Security on the Router

### a. Configure IP addressing on PCA

- [ ] Click **PCA → Desktop → IP Configuration**
- [ ] IP Address: `172.16.1.10`
- [ ] Subnet Mask: `255.255.255.0`
- [ ] Default Gateway: `172.16.1.1`

### b. Console into RTA

- [ ] **PCA → Desktop → Terminal → OK**, then press **Enter**.

### c-o. Configure RTA

Order matters here. The **hostname and domain name must be set before you generate the RSA keys**, and the **minimum password length should be set before you create passwords**.

```
Router> enable
Router# configure terminal

! c. Hostname
Router(config)# hostname RTA

! d. IP addressing on G0/0, then enable it
RTA(config)# interface g0/0
RTA(config-if)# ip address 172.16.1.1 255.255.255.0
RTA(config-if)# no shutdown
RTA(config-if)# exit

! e. Encrypt all plaintext passwords
RTA(config)# service password-encryption

! f. Minimum password length of 10
RTA(config)# security password min-length 10

! g. Strong enable secret (use your own)
RTA(config)# enable secret Str0ng!Secret26

! h. Disable DNS lookup
RTA(config)# no ip domain-lookup

! i. Domain name (case-sensitive for scoring)
RTA(config)# ip domain-name CCNA.com

! j. Local user with an encrypted password (use your own)
RTA(config)# username admin01 secret Adm1n!Passw0rd

! k. Generate 1024-bit RSA keys
RTA(config)# crypto key generate rsa
The name for the keys will be: RTA.CCNA.com
How many bits in the modulus [512]: 1024

! l. Block logins for 3 minutes after 4 failed attempts within 2 minutes
RTA(config)# login block-for 180 attempts 4 within 120

! m. VTY lines: SSH only, authenticate with local users
RTA(config)# line vty 0 4
RTA(config-line)# transport input ssh
RTA(config-line)# login local

! n. 6-minute EXEC timeout on the VTY lines
RTA(config-line)# exec-timeout 6
RTA(config-line)# end

! o. Save
RTA# copy running-config startup-config
```

> **Shortcut:** `crypto key generate rsa modulus 1024` skips the interactive prompt.

### What each command does (good for the exam)

| Command | Purpose |
|---|---|
| `service password-encryption` | Applies weak (type 7) encryption to plaintext passwords stored in the config, such as line passwords |
| `security password min-length 10` | Rejects any new password shorter than 10 characters |
| `enable secret ...` | Protects privileged EXEC with a hashed password (stronger than `enable password`) |
| `no ip domain-lookup` | Stops the router from trying to resolve mistyped commands as hostnames, which otherwise causes long delays |
| `ip domain-name CCNA.com` | Required, along with the hostname, to generate RSA keys |
| `username ... secret ...` | Creates a local account with a hashed password for SSH logins |
| `crypto key generate rsa` | Creates the key pair SSH needs to encrypt sessions |
| `login block-for 180 attempts 4 within 120` | Slows brute-force attacks: 4 failures in 120 s locks logins for 180 s |
| `transport input ssh` | Allows only SSH on the VTY lines (Telnet is refused) |
| `login local` | Authenticates against the local username database, not a shared line password |
| `exec-timeout 6` | Logs out an idle remote session after 6 minutes |

### p. Test the SSH connection from PCA

- [ ] **PCA → Desktop → Command Prompt**

```
C:\> ssh /?
Packet Tracer PC SSH
Usage: SSH -l username target

C:\> ssh -l admin01 172.16.1.1
Password: <your user password>
```

- [ ] You should land at the RTA prompt (`RTA>`). Type `enable` and use your enable secret to reach privileged EXEC.

---

## Step 2: Configure Basic Security on the Switch

Click **SW1 → CLI** tab.

```
Switch> enable
Switch# configure terminal

! b. Hostname
Switch(config)# hostname SW1

! c. Management IP on VLAN 1, then enable it
SW1(config)# interface vlan 1
SW1(config-if)# ip address 172.16.1.2 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit

! d. Default gateway
SW1(config)# ip default-gateway 172.16.1.1

! e. Disable all unused ports (keep F0/1 and G0/1 active)
SW1(config)# interface range f0/2-24, g0/2
SW1(config-if-range)# shutdown
SW1(config-if-range)# exit

! f. Encrypt plaintext passwords
SW1(config)# service password-encryption

! g. Strong enable secret (use your own)
SW1(config)# enable secret Str0ng!Secret26

! h. Disable DNS lookup
SW1(config)# no ip domain-lookup

! i. Domain name (case-sensitive for scoring)
SW1(config)# ip domain-name CCNA.com

! j. Local user with an encrypted password
SW1(config)# username admin01 secret Adm1n!Passw0rd

! k. Generate 1024-bit RSA keys
SW1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024

! l. VTY lines: SSH only, local authentication (switches have 16 VTY lines)
SW1(config)# line vty 0 15
SW1(config-line)# transport input ssh
SW1(config-line)# login local

! m. 6-minute EXEC timeout
SW1(config-line)# exec-timeout 6
SW1(config-line)# end

! n. Save
SW1# copy running-config startup-config
```

> **Why "all" VTY lines is `0 15` on the switch:** the router example used `0 4` (5 lines), but a Catalyst 2960 has 16 VTY lines. Configuring only 0–4 would leave lines 5–15 unsecured.

> **Why shut down unused ports:** an unused, enabled port is an open door. Someone could plug in a device unnoticed. `interface range` lets you apply the command to many ports at once.

---

## Verify Your Work

### Checklist

- [ ] PCA can ping RTA (`ping 172.16.1.1`) and SW1 (`ping 172.16.1.2`).
- [ ] From PCA, `ssh -l admin01 172.16.1.1` connects to **RTA**.
- [ ] From PCA, `ssh -l admin01 172.16.1.2` connects to **SW1**.
- [ ] On both devices, `show running-config` shows **no plaintext passwords** (look for `secret 5` / `password 7`).
- [ ] `show ip ssh` shows SSH **enabled** on both devices.
- [ ] On RTA, `show login` confirms the block-for settings.
- [ ] On SW1, `show ip interface brief` shows only F0/1 and G0/1 as up, and the rest `administratively down`.
- [ ] Both configs saved (`copy running-config startup-config` returned `[OK]`).
- [ ] Click **Check Results**: completion should be **100%**.

### Useful verification commands

```
RTA# show ip ssh
RTA# show login
RTA# show running-config | section line vty
SW1# show ip interface brief
SW1# show running-config | include username|domain|transport
```
<img width="1314" height="393" alt="Screenshot 2026-10-13 at 5 44 28 PM" src="https://github.com/user-attachments/assets/1f76945a-4760-47cc-bad6-f385bcf29b2c" />

