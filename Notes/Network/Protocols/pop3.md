POP3 Overview
# POP3 Protocol Notes

## Purpose
Protocol for retrieving emails from the server to a local client.

## Default Port
110 (POP3 server listens here).

## Key POP3 Commands
- **USER** – Identifies the username.
- **PASS** – Provides the user’s password.
- **STAT** – Requests the number of messages and total size.
- **LIST** – Lists all messages with their sizes.
- **RETR** – Retrieves the specified message.
- **DELE** – Marks a message for deletion.
- **QUIT** – Ends the session, applying any changes (like deletions).

## Example Telnet POP3 Session

### Connect to Server
```sh
telnet 10.10.247.157 110
```

### Login
```sh
USER strategos
PASS (hidden for security)
```

### Check Mailbox
```sh
STAT
```
Shows 3 messages totaling 1264 bytes.

```sh
LIST
```
Displays each message’s size (e.g., message 3 = 445 bytes).

### Retrieve Email
```sh
RETR 3
```
Pulls message 3 content.

### Close Connection
```sh
QUIT
```

## Key Observations

### POP3 & Security
Communications, including passwords, are in plain text, making it vulnerable to interception.

### POP3 Role
Complements SMTP by retrieving emails to the client.

### Wireshark Notes
Client commands in red, server responses in blue, useful for identifying POP3 interactions.