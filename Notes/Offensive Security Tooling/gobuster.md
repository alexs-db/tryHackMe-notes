# Gobuster Summary

Gobuster is a tool for brute-forcing web directories, DNS subdomains, vhosts, Amazon S3, and Google Cloud Storage. It’s commonly used in penetration testing and bug bounties, typically between the reconnaissance and scanning phases of ethical hacking.

## Key Concepts

- **Enumeration**: Listing all resources, accessible or not (e.g., directories).
- **Brute Force**: Trying every possibility (using wordlists) to find matches.

## Commands Overview

Use `gobuster --help` to see Gobuster’s commands and flags:

- **dir**: Directory enumeration
- **dns**: DNS subdomain enumeration
- **vhost**: Virtual host enumeration
- **s3 & gcs**: AWS and Google Cloud Storage buckets

## Common Flags

- `-t (--threads)`: Sets the number of threads, default is 10.
- `-w (--wordlist)`: Path to wordlist for brute-forcing.
- `--delay`: Delay between requests to avoid detection.
- `-o (--output)`: Saves results to a file.

## Example Command

```bash
gobuster dir -u "http://example.thm/" -w /usr/share/wordlists/dirb/small.txt -t 64
```

**Explanation**: This command brute forces directories at `http://example.thm/` using the wordlist `small.txt` with 64 threads for faster enumeration.

## Gobuster dir Mode Summary

Gobuster's dir mode allows users to brute-force and enumerate website directories and files to understand the structure, useful during penetration tests. It can identify if directories are accessible by returning HTTP status codes.

### Key Flags

- `-c (--cookies)`: Adds a session ID or other cookie with each request.
- `-x (--extensions)`: Specifies file extensions to search for, like .php or .js.
- `-H (--headers)`: Adds custom headers to each request.
- `-k (--no-tls-validation)`: Skips TLS certificate validation, useful for self-signed certs.
- `-P & -U`: Adds credentials for authenticated scans.
- `-s`: Shows only specified status codes (e.g., 200).
- `-b`: Excludes specific status codes from output.
- `-r`: Follows HTTP redirects (e.g., 301 or 302).

### Using dir Mode

```bash
gobuster dir -u "http://example.thm" -w /path/to/wordlist
```

### Examples

**Basic Directory Enumeration:**

```bash
gobuster dir -u "http://www.example.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r
```

Scans directories at `http://example.thm` using a wordlist and follows redirects.

**File Type Enumeration:**

```bash
gobuster dir -u "http://example.thm" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.js
```

Searches for `.php` and `.js` files in addition to directories.

This setup ensures Gobuster finds valuable paths and files on a target website.

## Gobuster DNS Mode Summary

In Gobuster's dns mode, users can brute-force subdomains of a domain to check if vulnerabilities exist in subdomains not present in the main domain. This can be crucial in penetration tests, as unpatched subdomains may offer opportunities for exploitation.

### Key Flags

- `-c (--show-cname)`: Displays CNAME records for each subdomain.
- `-i (--show-ips)`: Displays IP addresses for each resolved subdomain.
- `-r (--resolver)`: Specifies a custom DNS server for resolving.
- `-d (--domain)`: Specifies the target domain to enumerate.

### Using dns Mode

```bash
gobuster dns -d example.thm -w /path/to/wordlist
```

### Example

```bash
gobuster dns -d example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Command Breakdown**:
- `gobuster dns`: Activates DNS enumeration mode.
- `-d example.thm`: Sets the target domain.
- `-w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`: Uses a wordlist to guess subdomain names, appending each entry to the domain (e.g., all.example.thm).

This configuration lets Gobuster find potential subdomains that may reveal entry points into a system.

## Gobuster Vhost Mode Summary

In vhost mode, Gobuster brute-forces virtual hosts rather than subdomains. Virtual hosts allow multiple sites on the same server, making it possible to find hidden or less secure virtual sites on a target's IP.

### Key Differences from DNS Mode

- **Vhost Mode**: Combines the configured hostname (`-u` flag) with each entry in the wordlist and navigates to that URL.
- **DNS Mode**: Conducts DNS lookups for each wordlist entry combined with the base domain (`-d` flag).

### Main Flags

- `-u (--url)`: Sets the base URL (target IP or domain).
- `--append-domain`: Appends the domain to each word in the wordlist (e.g., word.example.com).
- `-m (--method)`: Sets the HTTP method, e.g., GET or POST.
- `-d (--domain)`: Appends a domain to each wordlist entry, forming a valid hostname.
- `--exclude-length`: Excludes responses based on length to avoid false positives.
- `-r (--follow-redirect)`: Follows HTTP redirects.

### Basic Command

```bash
gobuster vhost -u "http://example.thm" -w /path/to/wordlist
```

### Example Command

```bash
gobuster vhost -u "http://10.10.236.95" --domain example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320
```

**Command Breakdown**:
- `gobuster vhost`: Enables VHOST enumeration mode.
- `-u "http://10.10.236.95"`: Sets the target URL.
- `--domain example.thm`: Configures example.thm as the domain.
- `--append-domain`: Ensures Gobuster appends the domain to each entry.
- `--exclude-length`: Filters out responses of specific lengths to avoid false positives.

Each request changes the `Host:` header to probe different virtual hosts, e.g., blog.example.thm, shop.example.thm.