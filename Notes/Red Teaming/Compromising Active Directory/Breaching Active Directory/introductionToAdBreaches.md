# Introduction to Active Directory Breaches

Active Directory (AD) is used by approximately 90% of the Global Fortune 1000 companies. If an organisation's estate uses Microsoft Windows, you are almost guaranteed to find AD. Microsoft AD is the dominant suite used to manage Windows domain networks. However, since AD is used for Identity and Access Management of the entire estate, it holds the keys to the kingdom, making it a very likely target for attackers.

> **Tip:** For a more in-depth understanding of AD and how it works, please complete the room on AD basics first.

---

## Breaching Active Directory

Before exploiting AD misconfigurations for privilege escalation, lateral movement, and goal execution, you need initial access—specifically, a valid set of AD credentials. Due to the number of AD services and features, the attack surface for gaining initial credentials is significant. This guide discusses several avenues, but this is not an exhaustive list.

When searching for that first set of credentials, the permissions associated with the account are less important—even a low-privileged account is sufficient. The goal is simply to authenticate to AD and enable further enumeration.

---

## Learning Objectives

In this network, we will cover several methods to breach AD. This is not a complete list, as new techniques are discovered regularly. However, we will cover the following techniques to recover AD credentials:

- **NTLM Authenticated Services**
- **LDAP Bind Credentials**
- **Authentication Relays**
- **Microsoft Deployment Toolkit**
- **Configuration Files**

These techniques can be used during a security assessment by targeting internet-facing systems or by implanting a rogue device on the organisation's network.

---

## Connecting to the Network

### AttackBox

If you are using the Web-based AttackBox, you will be connected to the network automatically when you start the AttackBox from the room's page. You can verify this by running the `ping` command against the IP of the `THMDC.za.tryhackme.com` host. DNS configuration is still required, as Windows Networks use DNS to resolve hostnames to IPs.

To configure DNS, run:

```bash
sed -i '1s|^|nameserver $THMDCIP\n|' /etc/resolv-dnsmasq
```

> Replace `$THMDCIP` with the IP of THMDC in your network diagram.

Test DNS with:

```bash
nslookup thmdc.za.tryhackme.com
```

This should resolve to the IP of your Domain Controller (DC).

**Note:** DNS may be reset on the AttackBox every ~3 hours. If this occurs, rerun the command above. If your AttackBox terminates and you continue later, redo all DNS steps.

Also, note your VPN IP. Using `ifconfig` or `ip a`, find the IP of the `breachad` network adapter. This is your IP/interface for the attacks in the tasks.

---

### Other Hosts

If using your own attack machine, an OpenVPN configuration file will be generated once you join the room.

1. Go to your access page.
2. Select **BreachingAD** from the VPN servers (under the network tab) and download your configuration file.
3. Connect using an OpenVPN client. For Linux:

    ```bash
    sudo openvpn breachingad.ovpn
    ```

    Example output:

    ```
    Fri Mar 11 15:06:20 2022 OpenVPN 2.4.9 x86_64-redhat-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Apr 19 2020
    Fri Mar 11 15:06:20 2022 library versions: OpenSSL 1.1.1g FIPS  21 Apr 2020, LZO 2.08
    [...]
    Fri Mar 11 15:06:22 2022 /sbin/ip link set dev tun0 up mtu 1500
    Fri Mar 11 15:06:22 2022 /sbin/ip addr add dev tun0 10.50.2.3/24 broadcast 10.50.2.255
    Fri Mar 11 15:06:22 2022 /sbin/ip route add 10.200.4.0/24 metric 1000 via 10.50.2.1
    Fri Mar 11 15:06:22 2022 WARNING: this configuration may cache passwords in memory -- use the auth-nocache option to prevent this
    Fri Mar 11 15:06:22 2022 Initialization Sequence Completed
    ```

    The message **"Initialization Sequence Completed"** means you are connected. Refresh your access page to verify (look for a green tick next to Connected and your internal IP address).

> **Note:** You still need to configure DNS as shown above. The DC logs DNS requests, which may include your device's hostname.

---

### Kali

If using a Kali VM, Network Manager is likely used for DNS. Configure DNS via GUI:

- **Network Manager → Advanced Network Configuration → Your Connection → IPv4 Settings**
- Set your DNS IP to the IP for THMDC in the network diagram.
- Add another DNS (e.g., 1.1.1.1) to retain internet access.
- Run `sudo systemctl restart NetworkManager` and test DNS as above.

---

## Debugging DNS

DNS is integral to Active Directory testing, as Kerberos (one of the main AD authentication protocols) relies on DNS. Tickets cannot be associated with IPs, so DNS is essential.

If a task isn't working, first check your DNS configuration. Steps to debug:

1. **Follow the setup steps for your machine type.** If using a different OS, search for the equivalent configuration.
2. **Run:** `ping <THM DC IP>`  
   - If no response, the network is inactive. If the network says it's running but you get no response, contact THM support or wait for the network timer to reset.
3. **Run:** `nslookup za.tryhackme.com <THM DC IP>`  
   - If ping works but this doesn't, contact support or reset the network.
4. **Run:** `nslookup tryhackme.com`  
   - If you get a different response than in step 3, your DNS configuration is likely incorrect. Revisit the configuration steps. On Kali, ensure the DNS entry is the first in `/etc/resolv.conf`.

> **Note:** Hardcoding DNS entries in `/etc/hosts` may work for small networks but is impractical for large ones. Debugging DNS is a valuable skill for AD assessments.

---

## Final Notes

These AD networks are rated **medium**. If you are new to TryHackMe, consider starting elsewhere. AD is complex and requires a problem-solving mindset. If issues persist, provide detailed information when contacting support for efficient assistance.