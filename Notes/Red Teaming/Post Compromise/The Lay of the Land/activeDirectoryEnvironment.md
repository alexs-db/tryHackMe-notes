# What is the Active Directory (AD) Environment?

Active Directory (AD) is a Windows-based directory service that stores and provides data objects to the internal network environment. It allows for centralized management of authentication and authorization. AD contains essential information about the network and the environment, including users, computers, printers, etc. For example, AD might store user details such as job title, phone number, address, passwords, groups, and permissions.

The diagram below illustrates one possible example of how Active Directory can be designed. The AD controller is placed in a subnet for servers (referred to as the server network), while the AD clients are on a separate network. These clients can join the domain and use AD services via the firewall.

## Key Active Directory Components

Here is a list of Active Directory components to be familiar with:

- **Domain Controllers**: A Windows server that provides Active Directory services and controls the entire domain. It enables centralized user management, encrypts user data, and controls access to the network, including users, groups, policies, and computers. Domain controllers are high-value targets for attackers due to the sensitive information they store.
  
- **Organizational Units (OUs)**: Containers within the AD domain with a hierarchical structure.

- **Active Directory Objects**: These can represent a single user, a group, or a hardware component such as a computer or printer. Each domain holds a database containing object identity information that creates the AD environment, including:
  - **Users**: Security principals allowed to authenticate to machines in the domain.
  - **Computers**: A special type of user account.
  - **GPOs (Group Policy Objects)**: Collections of policies applied to other AD objects.

- **AD Domains**: A collection of Microsoft components within an AD network.

- **AD Forest**: A collection of domains that trust each other.

- **AD Service Accounts**: Includes built-in local users, domain users, and managed service accounts.

- **Domain Administrators**: Users with administrative privileges over the domain.

## Importance of Active Directory in Red Teaming

Once initial access has been achieved, identifying an AD environment in a corporate network is significant. The Active Directory environment provides joined users with a wealth of information about the network. As a red teamer, this information can be enumerated and leveraged to gain access to various details, which can then be used during the lateral movement stage.

For more information about the basics of Active Directory, consider exploring the TryHackMe room: **Active Directory Basics**.