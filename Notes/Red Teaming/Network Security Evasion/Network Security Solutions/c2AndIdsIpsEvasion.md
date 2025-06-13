## Malleable C2 Profiles in Pentesting Frameworks

Pentesting frameworks such as **Cobalt Strike** and **Empire** offer malleable Command and Control (C2) profiles. These profiles allow for fine-tuning to evade IDS/IPS systems. When using such frameworks, it is recommended to create a custom profile rather than relying on the default one.

### Key Variables to Customize

- **User-Agent:**  
    The default user-agent can expose your tool or framework. Always set the user-agent to something innocuous and verify your settings.

- **Sleep Time:**  
    Controls the callback interval between beacon check-ins. Adjusting this determines how often the infected system attempts to connect to the control server.

- **Jitter:**  
    Adds randomness to the sleep time, specified as a percentage. For example, a jitter of 30% results in a sleep time of ±30%, helping to further evade detection.

- **SSL Certificate:**  
    Using an authentic-looking SSL certificate can significantly improve your chances of evading detection. Investing time in this is worthwhile.

- **DNS Beacon:**  
    When using DNS to exfiltrate data, you can fine-tune DNS beacons by setting custom DNS servers and hostnames in the DNS query. The hostname can be used to hold exfiltrated data.

---

> **Example:**  
> The following Cobalt Strike guideline profile demonstrates how a profile can be structured.
