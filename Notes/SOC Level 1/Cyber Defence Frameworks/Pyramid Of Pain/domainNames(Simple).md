## Pyramid of Pain: Domain Names

Let's step up the Pyramid of Pain and move on to **Domain Names**. Notice the transition of colors—from green to teal.

Domain names map IP addresses to human-readable text. A domain name can be:

- A domain and top-level domain (e.g., `evilcorp.com`)
- A subdomain, domain, and top-level domain (e.g., `tryhackme.evilcorp.com`)

We won't cover the details of how the Domain Name System (DNS) works here. For more, see the ["DNS in Detail" Room](#).

### Why Are Domain Names Painful for Attackers?

Changing domain names is more difficult for attackers than changing IPs. They often need to:

- Purchase and register a new domain
- Modify DNS records

However, many DNS providers have loose standards and APIs, making it easier for attackers to change domains.

---

### Example: Malicious Sodinokibi C2 Domains

*(Insert screenshot or list of malicious domains here)*

Can you spot anything malicious in the above screenshot? Now, compare it to the legitimate website view below:

*(Insert screenshot of legitimate website here)*

---

### Punycode Attacks

This is an example of a **Punycode attack**, where attackers use lookalike domains to trick users.

> **What is Punycode?**  
> As per Jamf:  
> "Punycode is a way of converting words that cannot be written in ASCII, into a Unicode ASCII encoding."

For example, the domain `adıdas.de` has the Punycode:  
`http://xn--addas-o4a.de/`

Modern browsers (Internet Explorer, Chrome, Edge, Safari) are better at displaying these obfuscated characters.

---

### Detecting Malicious Domains

- Use proxy logs or web server logs to detect suspicious domains.
- Attackers often hide malicious domains using URL shorteners.

#### Common URL Shorteners Used by Attackers

- bit.ly
- goo.gl
- ow.ly
- s.id
- smarturl.it
- tiny.pl
- tinyurl.com
- x.co

You can preview the destination of a shortened link by appending a `+` to the URL (e.g., `bit.ly/example+`).  
**Note:** The examples below are non-existent.

---

## Viewing Connections in Any.run

[Any.run](https://any.run) is a sandboxing service that executes malware samples. You can review connections such as HTTP requests, DNS requests, or processes communicating with an IP address.

- **Networking Tab:** Located below the machine snapshot.

> **Caution:**  
> Do **not** visit any IP addresses or HTTP requests from a malware report. They are likely dangerous!

---

### Tabs in Any.run

#### HTTP Requests

Shows all HTTP requests since the sample was detonated. Useful for seeing resources retrieved from a webserver (e.g., droppers, callbacks).

*(Insert illustration of HTTP requests in Any.run view)*

#### Connections

Shows all communications made since detonation. Useful for identifying C2 traffic, FTP uploads/downloads, etc.

*(Insert illustration of connections in Any.run view)*

#### DNS Requests

Shows DNS requests made since detonation. Malware often checks for internet connectivity via DNS.

*(Insert illustration of DNS requests in Any.run view)*
