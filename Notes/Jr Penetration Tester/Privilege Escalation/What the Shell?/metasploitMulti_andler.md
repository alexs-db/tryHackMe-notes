# Metasploit Multi/Handler

Multi/Handler is a superb tool for catching reverse shells. It's essential if you want to use Meterpreter shells and is the go-to when using staged payloads.

Fortunately, it's relatively easy to use:

1. Open Metasploit with `msfconsole`.
2. Type `use multi/handler`, and press enter.

We are now primed to start a multi/handler session. Let's take a look at the available options using the `options` command:

There are three options we need to set: `PAYLOAD`, `LHOST`, and `LPORT`. These are all identical to the options we set when generating shellcode with Msfvenom -- a payload specific to our target, as well as a listening address and port with which we can receive a shell. Note that the `LHOST` must be specified here, as Metasploit will not listen on all network interfaces like netcat or socat will; it must be told a specific address to listen with (when using TryHackMe, this will be your `tun0` address). We set these options with the following commands:

```sh
set PAYLOAD <payload>
set LHOST <listen-address>
set LPORT <listen-port>
```

We should now be ready to start the listener!

Let's do this by using the `exploit -j` command. This tells Metasploit to launch the module, running as a job in the background.

You may notice that in the above screenshot, Metasploit is listening on a port under 1024. To do this, Metasploit must be run with sudo permissions.

When the staged payload generated in the previous task is run, Metasploit receives the connection, sending the remainder of the payload and giving us a reverse shell:

Notice that, because the multi/handler was originally backgrounded, we needed to use `sessions 1` to foreground it again. This worked as it was the only session running. Had there been other sessions active, we would have needed to use `sessions` to see all active sessions, then use `sessions <number>` to select the appropriate session to foreground. This number would also have been displayed in the line where the shell was opened (see "Command Shell session 1 opened").