# Introduction

This network is a continuation of the **Breaching AD**, **Enumerating AD**, and **Exploiting AD** networks. Please complete those networks before proceeding. We will discuss AD objects extensively—if you need a refresher, review the previous material.

Now that we have exploited AD and achieved positions from which we can execute our goals, we need to deploy persistence to prevent the blue team from easily removing our access. In this network, we will explore several methods for persisting in Active Directory (AD).

---

## AD Persistence

During an attack against AD, deploying persistence ensures that the blue team cannot simply remove us by rotating credentials. Compromising AD is a cyclic process; persistence should be established as we compromise the estate, not just at the end. This way, if one position is discovered and removed, we have fallback options. The persistence techniques used depend on the permissions and privileges acquired.

---

## Learning Objectives

We will cover several methods for persisting in AD. This is not a complete list, as available methods are highly situational and depend on the AD environment. The techniques covered include:

- AD Credentials and DCSync-ing
- Silver and Golden Tickets
- AD Certificates
- AD Security Identifiers (SIDs)
- Access Control Lists
- Group Policy Objects (GPOs)

---

## Connection to the Network

### AttackBox

If you are using the web-based AttackBox, you will be connected automatically when you start it from the room's page. Verify connectivity by running:

```bash
ping <THMDC.za.tryhackme.loc IP>
```

Note: The network suffix is `.loc` (not `.com`). DNS configuration is still required. Windows networks use DNS to resolve hostnames to IPs. To configure DNS, run:

```bash
sed -i '1s|^|nameserver $THMDCIP\n|' /etc/resolv-dnsmasq
```

Replace `$THMDCIP` with the IP of THMDC in your network diagram. Test DNS with:

```bash
nslookup thmdc.za.tryhackme.loc
```

This should resolve to your DC's IP.

> **Note:** DNS may reset every ~3 hours on the AttackBox. If so, repeat the command above. If your AttackBox terminates, redo all DNS steps.

Also, note your VPN IP using `ifconfig` or `ip a`. Record the IP of the `persistad` network adapter for use during attacks.

---

### Other Hosts

If using your own attack machine, an OpenVPN configuration file is generated after joining the room. Go to your access page, select **PersistingAD** from the VPN servers, and download your configuration file.

Connect using an OpenVPN client (example for Linux):

```bash
sudo openvpn persistingad.ovpn
```

Sample output:

```
Fri Mar 11 15:06:20 2022 OpenVPN 2.4.9 x86_64-redhat-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Apr 19 2020
Fri Mar 11 15:06:20 2022 library versions: OpenSSL 1.1.1g FIPS  21 Apr 2020, LZO 2.08
[....]
Fri Mar 11 15:06:22 2022 /sbin/ip link set dev tun0 up mtu 1500
Fri Mar 11 15:06:22 2022 /sbin/ip addr add dev tun0 10.50.2.3/24 broadcast 10.50.2.255
Fri Mar 11 15:06:22 2022 /sbin/ip route add 10.200.4.0/24 metric 1000 via 10.50.2.1
Fri Mar 11 15:06:22 2022 WARNING: this configuration may cache passwords in memory -- use the auth-nocache option to prevent this
Fri Mar 11 15:06:22 2022 Initialization Sequence Completed
```

The message **"Initialization Sequence Completed"** confirms you are connected. Refresh your access page to verify the connection and view your internal IP.

> **Note:** You must still configure DNS as shown above. The DC logs DNS requests, which may include your device's hostname.

---

### Kali

If using a Kali VM, Network Manager likely manages DNS. Configure DNS via the GUI:

- **Network Manager → Advanced Network Configuration → Your Connection → IPv4 Settings**
- Set your DNS IP to the IP for **THMCHILDDC** (see network diagram)
- Add another DNS (e.g., `1.1.1.1`) to maintain internet access
- Run `sudo systemctl restart NetworkManager` and test DNS as above

---

## Requesting Your Credentials

To simulate an AD breach, you will be provided with your first set of AD credentials. Once networking is set up, on your AttackBox, navigate to:

```
http://distributor.za.tryhackme.loc/creds
```

Click **"Get Credentials"** to receive your credential pair for initial access.

This pair provides RDP and SSH access to `THMWRK1.za.tryhackme.loc`, which acts as a jump host into the environment. Jump hosts are often targeted as they provide access to new network segments.

- Use Remmina or another Remote Desktop client for RDP (specify the domain: `za.tryhackme.loc`)
- For SSH access:

```bash
ssh za\\<AD Username>@thmwrk1.za.tryhackme.loc
```

When prompted, enter your password. SSH is generally faster than RDP for tasks.