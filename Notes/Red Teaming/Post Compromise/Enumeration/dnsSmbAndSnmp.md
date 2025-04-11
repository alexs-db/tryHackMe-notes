# DNS, SMB, and SNMP Enumeration

As we cover enumeration, it is a good idea to touch on DNS, SMB, and SNMP.

## DNS

We are all familiar with Domain Name System (DNS) queries where we can look up A, AAAA, CName, and TXT records, among others. If you want to brush up on your DNS knowledge, we suggest you visit the **DNS in Detail** room. If we can get a “copy” of all the records that a DNS server is responsible for answering, we might discover hosts we didn’t know existed.

One easy way to try DNS zone transfer is via the `dig` command. If you want to learn more about `dig` and similar commands, we suggest checking the **Passive Reconnaissance** room. Depending on the DNS server configuration, DNS zone transfer might be restricted. If it is not restricted, it should be achievable using:

```bash
dig -t AXFR DOMAIN_NAME @DNS_SERVER
```

The `-t AXFR` indicates that we are requesting a zone transfer, while `@` precedes the `DNS_SERVER` that we want to query regarding the records related to the specified `DOMAIN_NAME`.

---

## SMB

Server Message Block (SMB) is a communication protocol that provides shared access to files and printers. We can check shared folders using `net share`. Here is an example of the output:

```plaintext
user@TryHackMe$ net share

Share name   Resource                        Remark
-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\Windows                      Remote Admin
Internal     C:\Internal Files               Internal Documents
Users        C:\Users
The command completed successfully.
```

---

## SNMP

Simple Network Management Protocol (SNMP) was designed to help collect information about different devices on the network. It lets you know about various network events, from a server with a faulty disk to a printer out of ink. Consequently, SNMP can hold a trove of information for the attacker.

One simple tool to query servers related to SNMP is `snmpcheck`. You can find it on the AttackBox at the `/opt/snmpcheck/` directory. The syntax is quite simple:

```bash
/opt/snmpcheck/snmpcheck.rb MACHINE_IP -c COMMUNITY_STRING
```

If you would like to install `snmpcheck` on your local Linux box, consider the following commands:

```bash
git clone https://gitlab.com/kalilinux/packages/snmpcheck.git
cd snmpcheck/
gem install snmp
chmod +x snmpcheck-1.9.rb
```