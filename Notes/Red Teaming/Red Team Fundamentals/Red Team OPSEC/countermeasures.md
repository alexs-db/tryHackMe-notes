The final step is applying countermeasures. The US Department of Defense (DoD) Operations Security (OPSEC) Program Manual states:

> “Countermeasures are designed to prevent an adversary from detecting critical information, provide an alternative interpretation of critical information or indicators (deception), or deny the adversary’s collection system.”

Let’s revisit the two examples we presented in the Vulnerability Analysis task. 

1. **Example 1**: We considered the vulnerability of running Nmap, using the Metasploit framework, and hosting the phishing pages using the same public IP address. The countermeasure for this one seems obvious; use a different IP address for each activity. This way, you can ensure that if one activity was detected and the public IP address is blocked, the other activities can continue unaffected.

2. **Example 2**: We considered the vulnerability of an unsecured database used to store data received from a phishing page. From a risk assessment perspective, we considered it as high risk due to malicious third parties potentially looking for random easy targets. The countermeasure, in this case, would be to ensure that the database is adequately secured so that the data cannot be accessed except by authorized personnel.