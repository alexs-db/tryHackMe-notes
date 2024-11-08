FTP Overview
# FTP Notes

## Purpose
Transfer files between client and server.

## Default Port
21 (data transfer uses a separate connection).

## Basic FTP Commands
- `USER` – Enter username.
- `PASS` – Enter password.
- `RETR` – Download a file from the server.
- `STOR` – Upload a file to the server.

## Example Terminal Session

### Connect to Server
```sh
ftp 10.10.247.157
```

### Login as Anonymous
- **Username:** anonymous
- **Password:** None required

### List Files
```sh
ls
```
*Returns files like `coffee.txt`, `flag.txt`, `tea.txt`*

### Switch Mode to ASCII
```sh
type ascii
```
*For text files*

### Download File
```sh
get coffee.txt
```
*Warning about linefeeds received in ASCII mode, file may be corrupted*

## Key Observations
- **Command Translation:** `ls` on client = `LIST` command sent to the server.
- **Separate Connections:** Directory listings and file transfers use separate data connections.

## Wireshark Notes
- Client messages are red, server responses blue.
- Each directory listing and file download occurs on a new connection.