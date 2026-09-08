# NETWORKWALKS-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP
# 🔐 Cybersecurity Lab Environment Setup

Building a controlled virtual cybersecurity laboratory using **VirtualBox and Kali Linux** for ethical hacking, penetration-testing practice, and future security labs.

---

## 📌 Project Overview

This project focuses on setting up a virtual cybersecurity testing laboratory using VirtualBox and Kali Linux.

The lab uses a private **NAT Network** with the `10.0.0.0/24` subnet. Kali Linux is configured as the main security-testing machine with a static IP address of `10.0.0.2/24` and Internet access.

The setup follows the project requirements to create a repeatable environment that can later be expanded with additional virtual machines for authorized cybersecurity testing.

---

## 🎯 Objectives

The main objectives of this project are to:

- Install and configure VirtualBox.
- Install/import Kali Linux as the cybersecurity testing machine.
- Create a dedicated NAT Network using `10.0.0.0/24`.
- Configure Kali Linux with a consistent IP address.
- Configure Internet and DNS connectivity.
- Verify connectivity between Kali and the virtual network gateway.
- Document problems encountered during setup and how they were solved.
- Prepare the lab for future cybersecurity and penetration-testing exercises.

The original lab instructions require VirtualBox, Kali Linux, a `10.0.0.0/24` subnet, a NAT Network, Kali IP `10.0.0.2/24`, and full Internet access.

---

## 🛡️ Purpose of the Lab

This laboratory provides an isolated and controlled environment for learning cybersecurity and practicing authorized security testing.

Future activities may include:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Exploitation practice
- Security-tool experimentation

> ⚠️ **Ethical Use:** This lab should only be used for systems that you own or have explicit permission to test.

---

## 🏗️ Lab Architecture


───────────────
                    

---

## ⚙️ Lab Configuration

| Component | Configuration |
|---|---|
| Host OS | Windows |
| Hypervisor | VirtualBox 7.2.16 |
| Security OS | Kali Linux 2026.2 |
| Virtual Network | NAT Network |
| Network Name | CyberLab |
| Network Address | `10.0.0.0/24` |
| Kali IP Address | `10.0.0.2/24` |
| Default Gateway | `10.0.0.1` |
| Primary DNS | `8.8.8.8` |
| Secondary DNS | `1.1.1.1` |


---

# 🪜 Lab Setup Procedure

## Step 1. Install VirtualBox and Kali Linux

VirtualBox was used as the hypervisor, and Kali Linux 2026.2 was imported as the security-testing virtual machine.

The project instructions require VirtualBox and Kali Linux as the main components of the lab.

---

## Step 2. Create the NAT Network

A custom NAT Network named **CyberLab** was created using the `10.0.0.0/24` network.

The VirtualBox host was configured with:

```text
Network Name : CyberLab
Network      : 10.0.0.0/24
Gateway      : 10.0.0.1
DHCP         : Enabled
IPv6         : Disabled
```

The NAT Network was created from the Windows host using VirtualBox `VBoxManage`:

```cmd
VBoxManage natnetwork add --netname "CyberLab" --network "10.0.0.0/24" --enable --dhcp on
```

The network was verified with:

```cmd
VBoxManage list natnetworks
```

The assignment specifically requires creating a NAT Network using `10.0.0.0/24`. fileciteturn1file0L51-L58

---

## Step 3. Connect Kali Linux to CyberLab

Initially, the Kali VM was connected to the normal **NAT** adapter instead of the required NAT Network. This caused Kali to be unable to reach the `10.0.0.1` gateway.

The Kali VM network adapter was changed to:

```text
Attached to : NAT Network
Network     : CyberLab
```

The VM was attached to the NAT Network from the Windows host using `VBoxManage`.

After changing the adapter, Kali could successfully reach the virtual gateway.

---

## Step 4. Configure the Kali IP Address

Kali Linux was configured with the required static IPv4 address:

```text
IP Address : 10.0.0.2
Subnet     : 255.255.255.0
Gateway    : 10.0.0.1
```

The configuration was verified with:

```bash
ip addr
ip route
```

The expected routing table contained:

```text
default via 10.0.0.1 dev eth0
10.0.0.0/24 dev eth0
```

This matches the lab requirement for Kali to use `10.0.0.2/24`. fileciteturn1file0L15-L20

---

## Step 5. Test the Gateway

The virtual gateway was tested from Kali using:

```bash
ping -c 4 10.0.0.1
```

Result:

```text
4 packets transmitted, 4 received, 0% packet loss
```

This confirmed communication between Kali Linux and the CyberLab NAT Network gateway.

---

## Step 6. Test Internet Connectivity

Internet connectivity was tested using Google's public IP address:

```bash
ping -c 4 8.8.8.8
```

Result:

```text
4 packets transmitted, 4 received, 0% packet loss
```

This confirmed that Kali had Internet connectivity through the VirtualBox NAT Network.

---

## Step 7. Configure and Test DNS

After Internet connectivity was confirmed, DNS resolution was tested.

At first, DNS queries through the NAT Network gateway failed because Kali was using:

```text
nameserver 10.0.0.1
```

Testing showed:

```text
nslookup google.com

communications error to 10.0.0.1#53: connection refused
```

A direct DNS query to Google's DNS server worked successfully:

```bash
nslookup google.com 8.8.8.8
```

The DNS configuration was therefore changed to:

```text
nameserver 8.8.8.8
nameserver 1.1.1.1
```

After this change, hostname resolution and Internet access worked correctly:

```bash
getent ahostsv4 google.com
ping -4 -c 4 google.com
```

> **Note:** `/etc/resolv.conf` was generated by NetworkManager in the current Kali setup, so the DNS configuration may need to be made persistent after a reboot.

---

# 🔎 Lab Verification

| Test | Command | Expected Result |
|---|---|---|
| Check IP address | `ip addr` | Kali shows `10.0.0.2/24` |
| Check route | `ip route` | Default gateway is `10.0.0.1` |
| Test gateway | `ping -c 4 10.0.0.1` | Successful replies |
| Test Internet by IP | `ping -c 4 8.8.8.8` | Successful replies |
| Test DNS directly | `nslookup google.com 8.8.8.8` | Domain resolves |
| Test hostname resolution | `getent ahostsv4 google.com` | Google IP addresses displayed |
| Test Internet by domain | `ping -4 -c 4 google.com` | Successful replies |

### ✅ Final Working Network

```text
Kali IP       : 10.0.0.2/24
Gateway       : 10.0.0.1
NAT Network   : CyberLab
Network       : 10.0.0.0/24
DNS           : 8.8.8.8 / 1.1.1.1
Internet      : Working
DNS Resolution: Working
```

The lab instructions also specify checking the NAT Network, network settings, and Kali Internet connectivity when troubleshooting connectivity issues. fileciteturn1file1L110-L124

---

# 🐞 Problems Encountered & Solutions

## Problem 1. Kali Was Using Normal NAT Instead of NAT Network

### Problem

The Kali VM was initially configured with:

```text
Attached to: NAT
```

As a result, Kali could not reach the required `10.0.0.1` gateway while using the lab's `10.0.0.2/24` addressing.

### Solution

A custom NAT Network called **CyberLab** was created, and Kali's Adapter 1 was attached to it:

```text
Attached to: NAT Network
Network: CyberLab
```

After this change, the gateway became reachable.

---

## Problem 2. NAT Network Was Already Running

When the NAT Network was started manually, VirtualBox returned an error indicating that the DHCP server was already running.

This was not a new failure with the network configuration. It indicated that the CyberLab NAT Network was already active.

The network was verified using:

```cmd
VBoxManage list natnetworks
```

---

## Problem 3. DNS Resolution Failed

### Problem

Kali could ping IP addresses such as `8.8.8.8`, but domain names such as `google.com` initially failed to resolve.

### Investigation

The following tests were used:

```bash
ping -c 4 8.8.8.8
nslookup google.com 8.8.8.8
getent ahostsv4 google.com
```

The direct `nslookup` test to `8.8.8.8` succeeded, while the DNS server configured as `10.0.0.1` returned `connection refused`.

### Solution

The resolver configuration was changed to use public DNS servers:

```text
nameserver 8.8.8.8
nameserver 1.1.1.1
```

DNS resolution then worked normally.

---

# 💡 What I Learned

### 1. NAT vs NAT Network

I learned that normal NAT and NAT Network are different VirtualBox networking modes. The lab requires a NAT Network because it provides a suitable private network for connecting multiple virtual machines.

### 2. Virtual Machine Networking

I learned how the VirtualBox adapter mode affects the IP address, gateway, and communication between virtual machines.

### 3. Static IP Configuration

I learned how to configure and verify an IPv4 address, subnet mask, default gateway, and DNS settings in Kali Linux.

### 4. DNS Troubleshooting

I learned that having Internet access by IP does not necessarily mean DNS is working. Testing the gateway, Internet by IP, and DNS separately helped identify the actual problem.

### 5. Troubleshooting Method

I learned to troubleshoot networking step-by-step instead of changing many settings at once:

```text
Kali IP
   ↓
Gateway
   ↓
Internet by IP
   ↓
DNS Server
   ↓
Domain Name Resolution
```

### 6. Documentation

I learned that recording configuration details, commands, errors, solutions, and verification results makes a cybersecurity lab easier to reproduce and maintain.

---

# 🔐 Security & Ethical Use

This laboratory is intended for **education, cybersecurity training, and authorized security testing only**.

Any penetration-testing or exploitation activity should be performed only against machines and systems for which permission has been granted.

---

# 🔗 Tools & Resources

- [VirtualBox](https://www.virtualbox.org/)
- [Kali Linux](https://www.kali.org/get-kali/)
- [7-Zip](https://7-zip.org/)




---

# 👤 Author

**Name:** Gasper Boniphace 
**Project:** Cybersecurity Lab Setup  
**Week:** 01  
**Module:** Cybersecurity & Penetration Testing Lab Setup



