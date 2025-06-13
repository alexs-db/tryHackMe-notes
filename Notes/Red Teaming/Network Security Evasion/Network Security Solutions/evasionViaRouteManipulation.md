# Evasion via Route Manipulation

Evasion via route manipulation includes:

- Relying on source routing
- Using proxy servers

---

## Relying on Source Routing

Source routing allows you to force packets to use a specific route to reach their destination. Nmap supports this feature with the `--ip-options` flag, offering both loose and strict routing:

- **Loose routing**: Use `L` to specify intermediate hops.  
    Example:  
    ```
    --ip-options "L 10.10.10.50 10.10.50.250"
    ```
    This routes scan packets through the two provided IP addresses.

- **Strict routing**: Use `S` to specify every hop between your system and the target.  
    Example:  
    ```
    --ip-options "S 10.10.10.1 10.10.20.2 10.10.30.3"
    ```
    This ensures packets go via these three hops before reaching the target host.

---

## Using Proxy Servers

Proxy servers can help hide your source. Nmap provides the `--proxies` option, which accepts a comma-separated list of proxy URLs in the format `proto://host:port`. Supported protocols are `HTTP` and `SOCKS4`. Authentication is not currently supported.

**Example usage:**
```
nmap -sS --proxies HTTP://PROXY_HOST1:8080,SOCKS4://PROXY_HOST2:4153 MACHINE_IP
```
This command routes the scan through an HTTP proxy (`PROXY_HOST1`) and then a SOCKS4 proxy (`PROXY_HOST2`) before reaching the target.

> **Note:** Finding a reliable proxy may require some trial and error before you can depend on it to hide your Nmap scan source.

If you use a web browser to connect to the target, you can easily route your traffic via a proxy server. Most network tools also provide proxy settings to help hide your traffic source.