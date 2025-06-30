# Credentials Harvesting

This room covers the fundamental knowledge required for red teamers to leverage obtained credentials for lateral movement and accessing resources within an Active Directory (AD) environment. You will learn how to obtain, reuse, and impersonate user credentials.

Credential harvesting involves techniques for obtaining credentials such as login information, account names, and passwords. These credentials can be extracted from various locations, including clear-text files, the registry, memory dumps, and more.

## Benefits of Gaining Access to Credentials

- Enables access to additional systems (lateral movement)
- Makes detection of actions more difficult
- Provides opportunities to create and manage accounts to achieve red team objectives

## Learning Objectives

- Understand methods for extracting credentials from local Windows systems (SAM database)
- Learn how to access Windows memory and dump clear-text passwords and authentication tickets, both locally and remotely
- Introduction to Windows Credentials Manager and techniques for extracting credentials
- Explore methods for extracting credentials from Domain Controllers
- Enumerate the Local Administrator Password Solution (LAPS) feature
- Introduction to Active Directory attacks that lead to credential compromise