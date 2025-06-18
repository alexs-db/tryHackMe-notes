## Gaining Initial AD Credentials: OSINT and Phishing

Two popular methods for obtaining an initial set of Active Directory (AD) credentials are **Open Source Intelligence (OSINT)** and **Phishing**. These methods are only briefly mentioned here, as they are covered in more detail in other rooms.

---

### OSINT

OSINT involves discovering information that has been publicly disclosed. In the context of AD credentials, this can occur for several reasons:

- Users posting sensitive information, such as credentials, on public forums (e.g., Stack Overflow).
- Developers uploading scripts with hardcoded credentials to public repositories (e.g., GitHub).
- Credentials exposed in past data breaches, especially when employees use work accounts to register on external sites. Platforms like [HaveIBeenPwned](https://haveibeenpwned.com/) and [DeHashed](https://www.dehashed.com/) can help determine if work emails have been involved in known breaches.

By leveraging OSINT techniques, it may be possible to recover publicly disclosed credentials. However, these credentials may be outdated, so it's necessary to verify their validity. In **Task 3**, NTLM Authenticated Services are discussed as a way to test credentials.

> For a more detailed guide, see the dedicated room on **Red Team OSINT**.

---

### Phishing

Phishing is another effective method for breaching AD. It typically involves:

- Tricking users into entering their credentials on a malicious website.
- Convincing users to run a malicious application, such as a Remote Access Trojan (RAT), which operates in the user's context and allows impersonation of their AD account.

Phishing is a significant concern for both Red and Blue teams due to its prevalence and effectiveness.

> For more information, refer to the dedicated room on **Phishing**.
