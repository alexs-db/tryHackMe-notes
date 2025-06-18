# Enumerating Active Directory

This network is a continuation of the Breaching AD network. Please complete that network before starting here. We will discuss AD objects extensively—if you need a refresher, review the previous room. Now that we have our first set of valid Active Directory (AD) credentials, let's explore different methods for enumerating AD.

---

## AD Enumeration Overview

With valid AD credentials and the ability to authenticate, a wide range of enumeration possibilities opens up—even with low-privileged access. During a red team engagement, enumeration often leads to privilege escalation or lateral movement, allowing you to gain additional access. Enumeration and exploitation are closely linked: after exploiting an attack path, you enumerate again from your new privileged position.

---

## Learning Objectives

In this network, we will cover several methods for enumerating AD. This is not an exhaustive list, as available methods depend on the breach scenario. We will cover:

- The AD snap-ins of the Microsoft Management Console
- The `net` commands in Command Prompt
- The AD-RSAT cmdlets in PowerShell
- Bloodhound

---

## Connecting to the Network

### AttackBox

If you use the web-based AttackBox, you will be connected automatically when starting it from the room's page. Verify connectivity by running:

```bash
ping <THMDC_IP>
```

You still need to configure DNS. Windows networks use DNS to resolve hostnames. To configure DNS, run:

```bash
sed -i '1s|^|nameserver $THMDCIP\n|' /etc/resolv-dnsmasq
```

Replace `$THMDCIP` with the IP of THMDC in your network diagram. Test DNS with:

```bash
nslookup thmdc.za.tryhackme.com
```

This should resolve to your DC's IP.

> **Note:** DNS may reset on the AttackBox every ~3 hours. If this happens, repeat the command above. If your AttackBox terminates, redo all DNS steps when you return.

Make note of your VPN IP using `ifconfig` or `ip a`. The IP of the `enumad` network adapter is your IP for attack tasks.

---

### Other Hosts

If using your own attack machine, download the OpenVPN configuration file from your access page (select 'EnumeratingAD' under the network tab).

Connect using:

```bash
sudo openvpn adenumeration.ovpn
```

You should see:

```
Initialization Sequence Completed
```

This means you are connected. Verify on your access page (look for a green tick and your internal IP).

> **Note:** You still need to configure DNS as above. The DC logs DNS requests, which may include your device's hostname.

---

### Kali

If using a Kali VM, Network Manager is likely handling DNS. Configure DNS via:

- **Network Manager → Advanced Network Configuration → Your Connection → IPv4 Settings**
- Set DNS IP to THMDC's IP from the network diagram
- Add another DNS (e.g., 1.1.1.1) for internet access
- Run `sudo systemctl restart NetworkManager` and test DNS as above

---

## Requesting Your Credentials

To simulate an AD breach, you will be provided with your first set of AD credentials. Once networking is set up, on your AttackBox, go to:

```
http://distributor.za.tryhackme.com/creds
```

Click **Get Credentials** to receive your credential pair for initial access.

- This credential pair provides RDP and SSH access to `THMJMP1.za.tryhackme.com` (the jump host).
- Use Remmina or another Remote Desktop client for RDP (specify the domain `za.tryhackme.com`).
- Tasks 2 and 3 require RDP access.

For SSH access:

```bash
ssh za.tryhackme.com\\<AD Username>@thmjmp1.za.tryhackme.com
```

When prompted, enter your password. SSH is faster and can be used for Tasks 4, 5, and 6.
