
# Packet Tracer - Troubleshoot Connectivity Issues

## Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| R1 | G0/0 | 172.16.1.1 | 255.255.255.0 | N/A |
| R1 | G0/1 | 172.16.2.1 | 255.255.255.0 | N/A |
| R1 | S0/0/0 | 209.165.200.226 | 255.255.255.252 | N/A |
| R2 | G0/0 | 209.165.201.1 | 255.255.255.224 | N/A |
| R2 | S0/0/0 (DCE) | 209.165.200.225 | 255.255.255.252 | N/A |
| PC-01 | NIC | 172.16.1.3 | 255.255.255.0 | 172.16.1.1 |
| PC-02 | NIC | 172.16.1.4 | 255.255.255.0 | 172.16.1.1 |
| PC-A | NIC | 172.16.2.3 | 255.255.255.0 | 172.16.2.1 |
| PC-B | NIC | 172.16.2.4 | 255.255.255.0 | 172.16.2.1 |
| Web | NIC | 209.165.201.2 | 255.255.255.224 | 209.165.201.1 |
| DNS1 | NIC | 209.165.201.3 | 255.255.255.224 | 209.165.201.1 |
| DNS2 | NIC | 209.165.201.4 | 255.255.255.224 | 209.165.201.1 |

## Objectives

Troubleshoot and resolve connectivity issues, if possible. Otherwise, clearly document the issues so they can be escalated.

## Background / Scenario

Users are reporting that they cannot access the web server `www.cisco.pka` after a recent upgrade that included adding a second DNS server. You must determine the cause and attempt to resolve the issues for the users. Clearly document the issues and any solution(s). You do not have access to the devices in the cloud or the server `www.cisco.pka`. Escalate the problem if necessary.

> **Note:** Router R1 can only be accessed using **SSH** with username `Admin01` and password `cisco12345`. Router R2 is in the ISP cloud and is not accessible to you.

> **How to read the answers below:** This lab has faults planted in your specific Packet Tracer file, and I can't see them. So instead of guessing results, I give you the exact commands, what a *healthy* result looks like, and a table to record what *you* see. Items marked **(check your output)** must come from your own screen.

---

## How to Troubleshoot (Method)

Work **bottom-up**, the same order the exam expects:

1. **Local config:** Is the PC's IP, mask, gateway, and DNS correct? (Compare to the Addressing Table.)
2. **Default gateway:** Can the PC ping its gateway?
3. **Same-LAN and other-LAN hosts:** Can it ping peers on its own and the other LAN?
4. **Remote server by IP:** Can it ping/browse `209.165.201.2`?
5. **Name resolution:** If the IP works but the name doesn't, it's a **DNS** problem.

**Reading the results:**

| Result | What it points to |
|---|---|
| Can't ping default gateway | Wrong IP/mask/gateway on the PC, bad cable, or router interface down |
| Gateway OK, can't ping other LAN | Router interface down/misconfigured on the *other* LAN |
| Gateway OK, can't ping the web server by IP | Routing problem on R1 (missing/wrong route) or an ISP-side (R2) issue |
| Web works by IP but **not** by name | DNS server setting on the PC is wrong or the DNS server is unreachable |

---

## Step 1: Determine connectivity issues from PC-01

### a. Verify the IP configuration

- [ ] Open **Desktop → Command Prompt** on PC-01.
- [ ] Run:
  ```
  C:\> ipconfig
  ```
- [ ] Compare the IP address, mask, and default gateway to the Addressing Table (should be `172.16.1.3` / `255.255.255.0` / `172.16.1.1`).
- [ ] If anything is wrong, fix it in **Desktop → IP Configuration**, then re-run `ipconfig`. IP address for PC 1 is wrong. Should be 172.16.1.3 and is 172.168.1.3. 

### b. Test with pings

```
C:\> ping 172.16.1.1
C:\> ping 209.165.201.2
C:\> ping 172.16.1.4
C:\> ping 172.16.2.3
C:\> ping 172.16.2.4
```

| Ping target | Result **(check your output)** |
|---|---|
| Default gateway (172.16.1.1) | Successful |
| Web server (209.165.201.2) | Successful |
| PC-02 (172.16.1.4) | Successful |
| PC-A (172.16.2.3) | UnSuccessful |
| PC-B (172.16.2.4) | UnSuccessful |

### c. Test the web server

- [ ] **Desktop → Web Browser** → `http://www.cisco.pka`
- [ ] Then try `http://209.165.201.2`

| Test | Result **(check your output)** |
|---|---|
| PC-01 → www.cisco.pka | Successful |
| PC-01 → 209.165.201.2 | UnSuccessful |

### d. Document the issue and solution

Issue found:

SSH'd into the router through PC-01 
```
Password: cisco123
R1>en
R!# show ip interface brief
```
IP address for G0/1 was 172.16.3.1 which is wrong accroding to the table. 

```
R1# configure terminal
R1(config)# interface g0/1
R1(config-if)# ip address 172.16.2.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# end
R1# copy running-config startup-config
```

---

## Step 2: Determine connectivity issues from PC-02

### a. Verify the IP configuration

- [ ] `ipconfig` on PC-02. Expected: `172.16.1.4` / `255.255.255.0` / `172.16.1.1`.
- [ ] Correct anything that doesn't match, then re-run `ipconfig`.

The default gateway address was wrong (172.16.1.11) so corrected that (172.16.1.1). 

### b. Test with pings

```
C:\> ping 172.16.1.1
C:\> ping 209.165.201.2
C:\> ping 172.16.1.3
C:\> ping 172.16.2.3
C:\> ping 172.16.2.4
```

| Ping target | Result **(check your output)** |
|---|---|
| Default gateway (172.16.1.1) | Success |
| Web server (209.165.201.2) | Success |
| PC-01 (172.16.1.3) | Success |
| PC-A (172.16.2.3) | Success |
| PC-B (172.16.2.4) | Success |

### c. Test the web server

| Test | Result **(check your output)** |
|---|---|
| PC-02 → www.cisco.pka | Success |
| PC-02 → 209.165.201.2 | Success |

### d. Document the issue and solution

| Issue found | Solution applied |
|---|---|
| Wrong Gateway IP address| Corrected it to 176.16.1.1|

---

## Step 3: Determine connectivity issues from PC-A

### a. Verify the IP configuration

- [ ] `ipconfig` on PC-A. Expected: `172.16.2.3` / `255.255.255.0` / `172.16.2.1`.
- [ ] Correct anything that doesn't match, then re-run `ipconfig`.

### b. Test with pings

```
C:\> ping 209.165.201.2
C:\> ping 172.16.2.1
C:\> ping 172.16.2.4
C:\> ping 172.16.1.3
C:\> ping 172.16.1.4
```

| Ping target | Result **(check your output)** |
|---|---|
| Web server (209.165.201.2) | Success |
| Default gateway (172.16.2.1) | Success |
| PC-B (172.16.2.4) | Success |
| PC-01 (172.16.1.3) | Success |
| PC-02 (172.16.1.4) | Success |

### c. Test the web server

| Test | Result **(check your output)** |
|---|---|
| PC-A → www.cisco.pka | Success |
| PC-A → 209.165.201.2 | Success |

### d. Document the issue and solution

Nothing needed to do.

---

## Step 4: Determine connectivity issues from PC-B

### a. Verify the IP configuration

- [ ] `ipconfig` (or `ipconfig /all` to see the **DNS server**) on PC-B. Expected: `172.16.2.4` / `255.255.255.0` / `172.16.2.1`.
- [ ] Correct anything that doesn't match, then re-run.

### b. Test with pings

```
C:\> ping 209.165.201.2
C:\> ping 172.16.2.1
C:\> ping 172.16.2.3
C:\> ping 172.16.1.3
C:\> ping 172.16.1.4
```

| Ping target | Result **(check your output)** |
|---|---|
| Web server (209.165.201.2) | Success |
| Default gateway (172.16.2.1) | Success |
| PC-A (172.16.2.3) | Success |
| PC-01 (172.16.1.3) | Success |
| PC-02 (172.16.1.4) |Success |

### c. Test the web server

| Test | Result **(check your output)** |
|---|---|
| PC-B → www.cisco.pka | unSuccessful |

### d. Document the issue and solution

| Issue found | Solution applied |
|---|---|
| Pointing to wrong DNS server | COnfigured to point to DNS1 server|

### e. Could all the issues be resolved on PC-B and still make use of DNS2? If not, what would you need to do?

No. You can’t resolve everything on PC-B and still use DNS2, because DNS2 is in the ISP cloud and you can’t access or repair it. To use it, you’d have to escalate to the ISP to fix DNS2.

---

## Step 5: Verify connectivity

- [ ] From **every** PC (PC-01, PC-02, PC-A, PC-B), browse to `http://www.cisco.pka` and confirm it loads.
- [ ] Check the Packet Tracer **completion percentage** is **100%**.

If it isn't 100%, re-check that each device's IP configuration exactly matches the Addressing Table, including **subnet mask**, **default gateway**, and **DNS server**.

<img width="1049" height="335" alt="Screenshot 2026-10-12 at 4 24 29 PM" src="https://github.com/user-attachments/assets/a8033dbf-ee48-4ec6-a8bf-aff9d9c615af" />


*End of Document*
