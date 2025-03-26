# Password Attack Techniques

In this room, we will discuss the techniques that could be used to perform password attacks. We will cover various techniques such as dictionary, brute-force, rule-based, and guessing attacks. All the above techniques are considered active "online" attacks where the attacker needs to communicate with the target machine to obtain the password in order to gain unauthorized access to the machine.

---

## Password Cracking vs. Password Guessing

This section discusses password cracking terminology from a cybersecurity perspective. Additionally, we will explore significant differences between password cracking and password guessing. Finally, we'll demonstrate various tools used for password cracking, including **Hashcat** and **John the Ripper**.

### Password Cracking

Password cracking is a technique used for discovering passwords from encrypted or hashed data to plaintext data. Attackers may obtain the encrypted or hashed passwords from a compromised computer or capture them from transmitting data over the network. Once passwords are obtained, the attacker can utilize password attacking techniques to crack these hashed passwords using various tools.

Password cracking is considered one of the traditional techniques in penetration testing. The primary goal is to allow the attacker to escalate to higher privileges and gain access to a computer system or network.

### Password Guessing

Password guessing and password cracking are often commonly used by information security professionals. Both have different meanings and implications:

- **Password Guessing**:  
    Password guessing is a technique used to target online protocols and services. It is considered time-consuming and opens up the opportunity to generate logs for failed login attempts. A password guessing attack conducted on a web-based system often requires a new request to be sent for each attempt, which can be easily detected. It may also cause an account to be locked out if the system is designed and configured securely.

- **Password Cracking**:  
    Password cracking is a technique performed locally or on systems controlled by the attacker.