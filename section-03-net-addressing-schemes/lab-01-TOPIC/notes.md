
# Packet Tracer - Troubleshoot a Wireless Connection

## Objectives

- Identify and correct any misconfiguration of a wireless device.

## Background / Scenario

A small business owner learns that a wireless user is unable to access the network. All the PCs are configured with static IP addressing. Identify and resolve the issue.

> **How to read the answers below:** Packet Tracer generates some values per file, so anything marked **(check your output)** needs to be read from your own screen. Answers marked **(expected)** follow from the lab's design and should match, but confirm them as you go.

---

## Part 1: Verify connectivity

### Steps

- [ ] On each wireless PC, open **Desktop → Web Browser**.
- [ ] Enter `www.cisco.pka` in the URL box and click **Go**.
- [ ] Note which PCs fail to load the page.

> **Note:** All the devices require time to complete the boot process. Allow up to one minute before expecting a web response.

> **Question:** Which wireless PCs are unable to connect to the web server? 
>
> **Answer:** PC4 

---

## Part 2: Examine the IP configuration of the PCs

### Steps

- [ ] On the PC that can't connect, open **Desktop → Command Prompt**.
- [ ] Run:

```
C:\> ipconfig /all
```

> **Question:** What IP addressing information is available? 
>
> **Answer:** ipv4 address: 169.254.122.96 which is APIPA address subnet mask: 255.255.0.0

---

## Part 3: Examine the wireless settings on the wireless client

### Steps

- [ ] On the failing PC, open **Desktop → PC Wireless**.
- [ ] Click the **Connect** tab.
- [ ] Click **Refresh** as needed to display the list of available SSIDs.
- [ ] Record the SSID the PC is associated with (if any).

> **Question:** Which SSID is the client associated with?
>
> **Answer:** **(Academy)** 

---

## Part 4: Examine the wireless settings on the wireless router

### Steps

- [ ] From a **wired PC**, open **Desktop → Web Browser**.
- [ ] Browse to the wireless router's IP address (the wired PC's default gateway).
- [ ] Log in with username `admin` and password `admin`.

> **Question:** What IP address did you use? (Hint: default gateway)
>
> **Answer:** **192.168.1.1**

- [ ] On the **Basic Setup** page, examine the **DHCP Server Settings**.

> **Question:** Is DHCP enabled?
>
> **Answer:** **Yes** 

- [ ] Click the **Wireless** tab and examine the setup information.

> **Question:** What is the SSID? Does it match the SSID configured on the client?
>
> **Answer:** **Academy** The router's SSID should be **Academy** 

- [ ] Click the **Wireless Security** submenu and examine the security settings.

> **Question:** What is the wireless security mode? What is the passphrase?
>
> **Answer:** WPA2 Personal Encryption is AES and Passphrase is Cisco123

---

## Part 5: Make any necessary configuration changes on the wireless clients

### Steps

- [ ] On the failing PC, open **Desktop → PC Wireless**.
- [ ] Click the **Connect** tab.
- [ ] Select the **Academy** wireless network and click **Connect**.
- [ ] When prompted, enter the **Pre-shared Key** (passphrase) you recorded from the router, then click **Connect**.
- [ ] Open **Desktop → Web Browser** and browse to `www.cisco.pka`.

<img width="1432" height="590" alt="lab20--pic1" src="https://github.com/user-attachments/assets/830a74b6-cf02-4eed-a6da-b06dacb79b1f" />

