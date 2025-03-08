# Let's Setup a C2 Server

In order to gain a better understanding of what is required to set up and administer a C2 server, we will be using Armitage. As a reminder, Armitage is a GUI for the Metasploit Framework, and because of this, it has almost all aspects of a standard C2 framework.

> **Note:** In case you're using the AttackBox, you may skip to the Preparing our Environment section.

## Setting Up Armitage

### Downloading, Building, and Installing Armitage

First, we must clone the repository from Gitlab:

```bash
# Installing Armitage
root@kali$ git clone https://gitlab.com/kalilinux/packages/armitage.git && cd armitage
```

```plaintext
Cloning into 'armitage'...
remote: Enumerating objects: 760, done.
remote: Counting objects: 100% (160/160), done.
remote: Compressing objects: 100% (100/100), done.
remote: Total 760 (delta 55), reused 152 (delta 54), pack-reused 600
Receiving objects: 100% (760/760), 11.81 MiB | 8.55 MiB/s, done.
Resolving deltas: 100% (244/244), done.
```

Next up, we must build the current release; we can do so with the following command:

```bash
# Building Armitage
root@kali$ bash package.sh
```

```plaintext
+ ./gradlew assemble

> Task :armitage:compileJava
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
Note: Some input files use unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.

Deprecated Gradle features were used in this build, making it incompatible with Gradle 7.0.
Use '--warning-mode all' to show the individual deprecation warnings.
See https://docs.gradle.org/6.8/userguide/command_line_interface.html#sec:command_line_warnings

BUILD SUCCESSFUL in 12s
6 actionable tasks: 6 executed
+ for i in unix windows mac
+ '[' unix == mac ']'
+ mkdir -p release/unix
+ cp build.txt license.txt readme.txt whatsnew.txt release/unix
+ cp build/armitage.jar build/cortana.jar release/unix
+ cp -r dist/unix/armitage dist/unix/armitage-logo.png dist/unix/teamserver release/unix
+ '[' unix == mac ']'
+ for i in unix windows mac
+ '[' windows == mac ']'
+ mkdir -p release/windows
+ cp build.txt license.txt readme.txt whatsnew.txt release/windows
+ cp build/armitage.jar build/cortana.jar release/windows
+ cp -r dist/windows/armitage.exe release/windows
+ '[' windows == mac ']'
+ for i in unix windows mac
+ '[' mac == mac ']'
++ uname
+ '[' Linux '!=' Darwin ']'
+ echo 'Skipping macOS build because this is not running on Darwin'
Skipping macOS build because this is not running on Darwin
```

After the building process finishes, the release build will be in the `./releases/unix/` folder. You should check and verify that Armitage was able to be built successfully.

```bash
# Verifying Build
root@kali$ cd ./release/unix/ && ls -la
```

```plaintext
total 11000
drwxr-xr-x 2 root root    4096 Feb  6 20:20 .
drwxr-xr-x 4 root root    4096 Feb  6 20:20 ..
-rwxr-xr-x 1 root root      75 Feb  6 20:20 armitage
-rw-r--r-- 1 root root 4334705 Feb  6 20:20 armitage.jar
-rw-r--r-- 1 root root   25985 Feb  6 20:20 armitage-logo.png
-rw-r--r-- 1 root root     282 Feb  6 20:20 build.txt
-rw-r--r-- 1 root root 6778470 Feb  6 20:20 cortana.jar
-rw-r--r-- 1 root root    1491 Feb  6 20:20 license.txt
-rw-r--r-- 1 root root    4385 Feb  6 20:20 readme.txt
-rwxr-xr-x 1 root root    2665 Feb  6 20:20 teamserver
-rw-r--r-- 1 root root   85945 Feb  6 20:20 whatsnew.txt
```

In this folder, there are two key files that we will be using:

- **Teamserver**: This is the file that will start the Armitage server that multiple users will be able to connect to. This file takes two arguments:
    - **IP Address**: Your fellow Red Team Operators will use the IP Address to connect to your Armitage server.
    - **Shared Password**: Your fellow Red Team Operators will use the Shared Password to access your Armitage server.

- **Armitage**: This is the file you will be using to connect to the Armitage Teamserver. Upon executing the binary, a new prompt will open up, displaying connection information and your username (this should be treated as a nickname, not a username for authentication) and password.

## Preparing our Environment

Before we can launch Armitage, we must do a few pre-flight checks to ensure Metasploit is configured properly. Armitage relies heavily on Metasploit's Database functionality, so we must start and initialize the database before launching Armitage. In order to do so, we must execute the following commands:

```bash
# Starting PostgreSQL
root@kali$ systemctl start postgresql && systemctl status postgresql
```

```plaintext
postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Sun 2022-02-06 20:16:03 GMT; 41min ago
    Process: 1587 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
 Main PID: 1587 (code=exited, status=0/SUCCESS)

Feb 06 20:16:03 ip-10-10-142-239 systemd[1]: Starting PostgreSQL RDBMS...
Feb 06 20:16:03 ip-10-10-142-239 systemd[1]: Started PostgreSQL RDBMS.
```

Lastly, we must initialize the Database so that Metasploit can use it. It's important to note that you cannot be the root user when attempting to initialize the Metasploit Database. On the AttackBox, you must use the Ubuntu user.

```bash
# Initializing PostgreSQL Database
user@kali$ msfdb --use-defaults delete
```

```plaintext
Stopping database at /home/ubuntu/.msf4/db
Deleting all data at /home/ubuntu/.msf4/db
MSF web service is no longer running
```

```bash
user@kali$ msfdb --use-defaults init
```

```plaintext
Creating database at /home/ubuntu/.msf4/db
Starting database at /home/ubuntu/.msf4/db...success
Creating database users
Writing client authentication configuration file /home/ubuntu/.msf4/db/pg_hba.conf
Stopping database at /home/ubuntu/.msf4/db
Starting database at /home/ubuntu/.msf4/db...success
Creating initial database schema
Generating SSL key and certificate for MSF web service
Attempting to start MSF web service...failed
[!] MSF web service does not appear to be started.
Please see /home/ubuntu/.msf4/logs/msf-ws.log for additional details.
```

After initialization is completed, we can finally start the Armitage Team Server.

## Starting and Connecting to Armitage

```bash
# Starting Armitage's Team Server
root@kali$ cd /opt/armitage/release/unix && ./teamserver YourIP P@ssw0rd123
```

```plaintext
[*] Generating X509 certificate and keystore (for SSL)
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
[*] Starting RPC daemon
[*] MSGRPC starting on 127.0.0.1:55554 (NO SSL):Msg...
[*] MSGRPC backgrounding at 2022-02-06 17:47:08 -0500...
[*] MSGRPC background PID 2083
[*] sleeping for 20s (to let msfrpcd initialize)
[*] Starting Armitage team server
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
[*] Use the following connection details to connect your clients:
                Host: 127.0.0.2
                Port: 55553
                User: msf
                Pass: P@ssw0rd123

[*] Fingerprint (check for this string when you connect):
                d211e51c8886113433f63b588fd5ccfc9e323059
[+] hacking is such a lonely thing, until now
```

Once your Teamserver is up and running, we can now start the Armitage client. This is used to connect to the Teamserver and displays the GUI to the user.

```bash
# Starting Armitage
root@kali$ cd /opt/armitage/release/unix && ./armitage
```

```plaintext
[*] Used the incumbent: 10.10.69.193
[*] Starting Cortana on 10.10.69.193
[*] Starting Cortana on 10.10.69.193
[*] Creating a default reverse handler... 0.0.0.0:8836
```

When operating a C2 Framework, you never want to expose the management interface publicly; You should always listen on a local interface, never a public-facing one. This complicates access for fellow operators. Fortunately, there is an easy solution for this. For operators to gain access to the server, you should create a new user account for them and enable SSH access on the server, and they will be able to SSH port forward TCP/55553. Armitage explicitly denies users listening on 127.0.0.1; this is because it is essentially a shared Metasploit server with a "Deconfliction Server" that when multiple users are connecting to the server, you're not seeing everything that your other users are seeing. With Armitage, you must listen on your tun0/eth0 IP Address.

Modify the Host IP Address to whatever you set in the previous step, "Starting Armitage's Team Server".

After clicking "Connect", you will be prompted to enter a nickname. You can set this to whatever you like; only your fellow Red Team Operators will see it.

After a moment or two, the Armitage UI should open up, until we start interacting with remote systems; it will look bare. In the next upcoming task, we will be exploiting a vulnerable virtual machine to get you more accustomed to the Armitage UI and how it can be used.

Now that Armitage is set up and working correctly, in the next task, we will learn more about securely accessing Armitage (as described above), creating listeners, various listener types, generating payloads, and much more!