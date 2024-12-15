Another method system administrators can use to increase the privilege level of a process or binary is “Capabilities”. Capabilities help manage privileges at a more granular level. For example, if the SOC analyst needs to use a tool that needs to initiate socket connections, a regular user would not be able to do that. If the system administrator does not want to give this user higher privileges, they can change the capabilities of the binary. As a result, the binary would get through its task without needing a higher privilege user.

The capabilities man page provides detailed information on its usage and options.

### Listing Enabled Capabilities

We can use the `getcap` tool to list enabled capabilities.

When run as an unprivileged user, `getcap -r /` will generate a huge amount of errors, so it is good practice to redirect the error messages to `/dev/null`.

Please note that neither `vim` nor its copy has the SUID bit set. This privilege escalation vector is therefore not discoverable when enumerating files looking for SUID.

### Using GTFObins

GTFObins has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities.

We notice that `vim` can be used with the following command and payload:

```
# Command and payload here
```

This will launch a root shell as seen below:

```
# Root shell output here
```