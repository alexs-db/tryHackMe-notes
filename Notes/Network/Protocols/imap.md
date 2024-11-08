IMAP Overview
# IMAP (Internet Message Access Protocol)

## Purpose
IMAP allows multiple devices to synchronize and access emails without deleting them from the server.

## Default Port
- **143**: IMAP server listens here.

## Best For
Users who access emails from multiple devices, needing mailbox synchronization.

## Key IMAP Commands
- **LOGIN**: Authenticates user credentials.
- **SELECT**: Chooses the mailbox folder to work within (e.g., inbox).
- **FETCH**: Retrieves specific email content (e.g., message body).
- **MOVE**: Moves messages to another mailbox.
- **COPY**: Copies messages to a selected mailbox.
- **LOGOUT**: Ends the IMAP session.

## Example Telnet IMAP Session

### Connect to Server
```sh
telnet 10.10.41.192 143
```

### Login
```sh
Username: LOGIN strategos
```

### Select Inbox
```sh
SELECT inbox
```
Shows 4 existing messages.

### Fetch Email
```sh
FETCH 3 body[]
```
Retrieves the full content of message 3.

### Close Connection
```sh
LOGOUT
```

## Key Observations

### IMAP vs POP3
- **IMAP**: Keeps emails on the server, allowing synchronization.
- **POP3**: Typically downloads and removes emails.

### Security Note
Like POP3, IMAP commands and responses are visible in clear text, making interception possible.

### Wireshark Notes
- **Client commands**: In red.
- **Server responses**: In blue.
- Useful for tracking IMAP interactions.

## Common Protocols and Ports

| Protocol | Transport Protocol | Default Port Number |
|----------|---------------------|---------------------|
| TELNET   | TCP                 | 23                  |
| DNS      | UDP or TCP          | 53                  |
| HTTP     | TCP                 | 80                  |
| HTTPS    | TCP                 | 443                 |
| FTP      | TCP                 | 21                  |
| SMTP     | TCP                 | 25                  |
| POP3     | TCP                 | 110                 |
| IMAP     | TCP                 | 143                 |