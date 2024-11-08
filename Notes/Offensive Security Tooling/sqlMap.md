# Summary of SQL Injection Vulnerability Exploitation

Applications use SQL queries to retrieve, modify, or store data in a database. A typical login page will use an SQL query to verify a user’s credentials:

```sql
SELECT * FROM users WHERE username = 'John' AND password = 'Un@detectable444';
```

This query searches for a user named "John" with the password "Un@detectable444." If both match, access is granted.

## Vulnerability and Exploitation

When input fields (like the password) aren’t properly validated, an attacker can inject SQL code to manipulate the query and bypass security. For example, if the site does not sanitize input correctly, an attacker might enter:

- **Username:** John
- **Password:** abc' OR 1=1;-- -

This transforms the query into:

```sql
SELECT * FROM users WHERE username = 'John' AND password = 'abc' OR 1=1;-- -';
```

### How It Works

- **OR Condition:** This query checks if the password is "abc" (which is incorrect) or if 1=1.
- **1=1 is Always True:** Since 1=1 is always true, the query succeeds even with the wrong password.
- **Comment (-- -):** The `-- -` turns the rest of the query into a comment, so the database ignores it.

This allows the attacker to log into the "John" account without knowing his password.

## Automated SQL Injection Tool

Carrying out an SQL injection attack involves discovering the SQL injection vulnerability inside the application and manipulating the database. However, doing this manually can take time and effort.

SQLMap is an automated tool designed to detect and exploit SQL injection vulnerabilities in web applications, making this process much easier. While it’s included in some Linux distributions, SQLMap can be installed separately if needed.

To use SQLMap, open your Linux terminal. Entering `--help` with the SQLMap command will display all available flags. Alternatively, use the `--wizard` flag, which prompts SQLMap to guide you through the process, making it beginner-friendly:

### Example of Running SQLMap in Wizard Mode

```bash
user@ubuntu:~$ sqlmap --wizard
```

This command initiates the SQLMap wizard, prompting you to input a target URL. The tool then identifies and confirms potential vulnerabilities.

Once a URL is identified as vulnerable to SQL injection, SQLMap can be used with various flags to extract specific data. For instance:

### Fetch Database Names

```bash
sqlmap -u http://sqlmaptesting.thm/search?cat=1 --dbs
```

### List Tables in a Database

```bash
sqlmap -u http://sqlmaptesting.thm/search?cat=1 -D database_name --tables
```

### Dump Table Records

```bash
sqlmap -u http://sqlmaptesting.thm/search?cat=1 -D database_name -T table_name --dump
```

In this example, `cat=1` in the URL represents a GET parameter, often susceptible to SQL injection. SQLMap quickly identifies vulnerabilities, such as boolean-based, error-based, and time-based SQL injections, allowing you to extract data accordingly.

For scenarios where POST requests are used (e.g., login forms), save the intercepted POST request to a text file and input it to SQLMap:

### Testing an Intercepted POST Request

```bash
user@ubuntu:~$ sqlmap -r intercepted_request.txt
```

This command enables SQLMap to test the POST request for SQL injection vulnerabilities.
