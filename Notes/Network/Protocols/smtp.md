SMTP Overview
# SMTP Notes

## Purpose
Protocol for sending emails from a client to a server and between servers.

## Default Port
25 (SMTP server listens here).

## SMTP Commands
- **HELO/EHLO** – Initiates conversation with the server.
- **MAIL FROM** – Specifies the sender’s email address.
- **RCPT TO** – Specifies the recipient’s email address.
- **DATA** – Begins the email message body.
- **. (dot)** – Ends the email message.

## Example Telnet Email Session

### Connect to Server
```sh
telnet 10.10.247.157 25
```

### Initiate with HELO
```sh
HELO client.thm
```

### Specify Sender
```sh
MAIL FROM: <user@client.thm>
```

### Specify Recipient
```sh
RCPT TO: <strategos@server.thm>
```

### Send Email Content
```sh
DATA
# followed by the message, ending with . on a new line.
```

### Close Connection
```sh
QUIT
```

## Key Observations
- **Protocol Flow**: Similar to addressing and sending a package, with sender and recipient info required before sending content.
- **Command Translation**: Shows the behind-the-scenes commands your email client uses.
- **Wireshark Notes**: Client messages in red, server responses in blue for easy tracking of SMTP interactions.