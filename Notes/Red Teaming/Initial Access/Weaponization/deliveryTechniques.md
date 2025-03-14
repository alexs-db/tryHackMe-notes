# Delivery Techniques

Delivery techniques are crucial for obtaining initial access. They must appear professional, legitimate, and convincing to the victim to increase the likelihood of success.

## Email Delivery

One of the most common methods involves sending a phishing email with a malicious link or attachment. The goal is to persuade the victim to visit a malicious website or download and execute a malicious file, thereby granting initial access to the victim's network or host.

### Infrastructure Considerations
Red teamers should have a dedicated phishing infrastructure. Depending on the engagement requirements, this may include:
- Setting up an email server with proper configurations such as:
  - **DomainKeys Identified Mail (DKIM)**
  - **Sender Policy Framework (SPF)**
  - **DNS Pointer (PTR) records**
- Using third-party email services with good reputations, such as:
  - Google Gmail
  - Outlook
  - Yahoo

### Using Compromised Email Accounts
Another effective technique involves using a compromised email account within a company to send phishing emails internally or externally. Methods to compromise an email account include:
- **Phishing attacks**
- **Password spraying attacks**

## Web Delivery

Hosting malicious payloads on a web server controlled by red teamers is another technique. The web server must comply with security best practices, such as:
- Maintaining a clean reputation for its domain name
- Implementing a **TLS (Transport Layer Security) certificate**

### Additional Techniques
- **Social engineering**: Convincing the victim to visit or download a malicious file.
- **URL shorteners**: Used to obfuscate the true nature of the link.
- **Zero-day exploits**: Leveraging vulnerabilities in software such as Java or web browsers to compromise victim machines.

## USB Delivery

This method involves the victim physically plugging in a malicious USB device. It is particularly effective at conferences or events where adversaries can distribute USBs.

### Common USB-Based Attacks
- **Rubber Ducky**: Emulates a keyboard to execute malicious commands.
- **USBHarpoon**: Exploits USB communication vulnerabilities.
- **O.MG Cable**: A charging cable embedded with malicious payloads.

### Security Countermeasures
Many organizations mitigate USB-based attacks by:
- **Disabling USB ports** within their corporate environment.
- **Implementing strict security policies** to prevent unauthorized USB usage.

By understanding and implementing these delivery techniques, red teamers can enhance their initial access strategies while security teams can better defend against such attacks.