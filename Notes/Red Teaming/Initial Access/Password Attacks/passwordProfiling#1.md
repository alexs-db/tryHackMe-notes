# Password Profiling

Having a good wordlist is critical to carrying out a successful password attack. It is important to know how you can generate username lists and password lists. In this section, we will discuss creating targeted username and password lists. We will also cover various topics, including default, weak, leaked passwords, and creating targeted wordlists.

## Default Passwords

Before performing password attacks, it is worth trying a couple of default passwords against the targeted service. Manufacturers set default passwords with products and equipment such as switches, firewalls, and routers. There are scenarios where customers don't change the default password, which makes the system vulnerable. Thus, it is a good practice to try out `admin:admin`, `admin:123456`, etc. If we know the target device, we can look up the default passwords and try them out. For example, suppose the target server is a Tomcat, a lightweight, open-source Java application server. In that case, there are a couple of possible default passwords we can try: `admin:admin` or `tomcat:admin`.

Here are some websites that provide default passwords for various products:

- [CIRT Default Passwords](https://cirt.net/passwords)
- [Default Password Info](https://default-password.info/)
- [Data Recovery Default Passwords](https://datarecovery.com/rd/default-passwords/)

## Weak Passwords

Professionals collect and generate weak password lists over time and often combine them into one large wordlist. Lists are generated based on their experience and what they see in pentesting engagements. These lists may also contain leaked passwords that have been published publicly. Here are some common weak password lists:

- [SkullSecurity Passwords](https://www.skullsecurity.org/wiki/Passwords) - Includes the most well-known collections of passwords.
- [SecLists](https://github.com/danielmiessler/SecLists) - A huge collection of all kinds of lists, not only for password cracking.

## Leaked Passwords

Sensitive data such as passwords or hashes may be publicly disclosed or sold as a result of a breach. These public or privately available leaks are often referred to as "dumps." Depending on the contents of the dump, an attacker may need to extract the passwords out of the data. In some cases, the dump may only contain hashes of the passwords and require cracking to gain the plain-text passwords. The following are some common password lists that include weak and leaked passwords:

- `SecLists/Passwords/Leaked-Databases`

## Combined Wordlists

If we have more than one wordlist, we can combine these wordlists into one large file. This can be done as follows using `cat`:

```bash
cat file1.txt file2.txt file3.txt > combined_list.txt
```

To clean up the generated combined list and remove duplicated words, we can use `sort` and `uniq` as follows:

```bash
sort combined_list.txt | uniq -u > cleaned_combined_list.txt
```

## Customized Wordlists

Customizing password lists is one of the best ways to increase the chances of finding valid credentials. We can create custom password lists from the target website. Often, a company's website contains valuable information about the company and its employees, including emails and employee names. In addition, the website may contain keywords specific to what the company offers, including product and service names, which may be used in an employee's password.

Tools such as `Cewl` can be used to effectively crawl a website and extract strings or keywords. `Cewl` is a powerful tool to generate a wordlist specific to a given company or target. Consider the following example:

```bash
cewl -w list.txt -d 5 -m 5 http://thm.labs
```

- `-w` writes the contents to a file (in this case, `list.txt`).
- `-m 5` gathers strings (words) that are 5 characters or more.
- `-d 5` sets the depth level of web crawling/spidering (default is 2).
- `http://thm.labs` is the URL to crawl.

As a result, we should now have a decently sized wordlist based on relevant words for the specific enterprise, like names, locations, and business lingo. Similarly, the wordlist created could be used to fuzz for usernames.

**Task:** Apply what we discussed using `Cewl` against `https://clinic.thmredteam.com/` to parse all words and generate a wordlist with a minimum length of 8. Note that we will use this wordlist later in another task!

## Username Wordlists

Gathering employees' names during the enumeration stage is essential. We can generate username lists from the target's website. For the following example, we'll assume we have a `{first name}` `{last name}` (e.g., John Smith) and a method of generating usernames:

- `{first name}`: `john`
- `{last name}`: `smith`
- `{first name}{last name}`: `johnsmith`
- `{last name}{first name}`: `smithjohn`
- First letter of the `{first name}{last name}`: `jsmith`
- First letter of the `{last name}{first name}`: `sjohn`
- First letter of the `{first name}.{last name}`: `j.smith`
- First letter of the `{first name}-{last name}`: `j-smith`

Thankfully, there is a tool called `username_generator` that can help create a list with most of the possible combinations if we have a first name and last name.

### Cloning the Tool

```bash
git clone https://github.com/therodri2/username_generator.git
cd username_generator
```

### Using the Tool

Run the following command to see the help message and optional arguments:

```bash
python3 username_generator.py -h
```

**Example Output:**

```plaintext
usage: username_generator.py [-h] -w wordlist [-u]

Python script to generate user lists for bruteforcing!

optional arguments:
    -h, --help            show this help message and exit
    -w wordlist, --wordlist wordlist
                                                Specify path to the wordlist
    -u, --uppercase       Also produce uppercase permutations. Disabled by default
```

Now let's create a wordlist containing the full name "John Smith" in a text file. Then, we'll run the tool to generate the possible combinations of the given full name:

```bash
echo "John Smith" > users.lst
python3 username_generator.py -w users.lst
```

**Example Output:**

```plaintext
john
smith
j.smith
j-smith
j_smith
j+smith
jsmith
smithjohn
```

This is just one example of a custom username generator. Feel free to explore more options or even create your own in the programming language of your choice!