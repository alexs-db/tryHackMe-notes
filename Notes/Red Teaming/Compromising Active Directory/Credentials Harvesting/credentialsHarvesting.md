# Credentials Harvesting

**Credentials harvesting** refers to gaining access to user and system credentials. This technique involves searching for or stealing stored credentials, including methods like network sniffing, where an attacker captures credentials as they are transmitted.

Credentials can be found in various forms, such as:

- **Account details:** Usernames and passwords
- **Hashes:** NTLM hashes, etc.
- **Authentication tickets:** Ticket Granting Ticket (TGT), Ticket Granting Server (TGS)
- **Other sensitive information:** Private keys or anything that helps log in to a system

Generally, there are two types of credential harvesting:

- **External:** Often involves phishing emails or other techniques to trick users into revealing their credentials. For more on phishing, try the [THM Phishing room](https://tryhackme.com/room/phishing).
- **Internal:** Uses different approaches within a compromised network.

---

In this room, the focus is on harvesting credentials from an **internal perspective**, where a threat actor has already compromised a system and gained initial access.

A Windows Server 2019 configured as a Domain Controller is provided. To follow along, deploy the machine and proceed to the next task.

You can access the machine either in-browser or via RDP using the credentials below:

- **Machine IP:** `MACHINE_IP`
- **Username:** `thm`
- **Password:** `Passw0rd!`

> **Note:** Ensure you deploy the AttackBox, as it is required for the attacks discussed in this room.