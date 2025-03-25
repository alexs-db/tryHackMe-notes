# Using GoPhish for Phishing Campaigns

This task will guide you through setting up GoPhish, sending a phishing campaign, and capturing user credentials from a spoofed website.

## Setting Up GoPhish

1. **Launch the Virtual Machine**  
    - Click the green **Start Machine** button on the right.  
    - Once loaded, open the GoPhish login page:  
      - URL: `https://LAB_WEB_URL.p.thmlabs.com:8443`  
      - Or, if connected to the TryHackMe VPN: `https://MACHINE_IP`  
    - If you receive an Nginx error, wait 30 seconds and try again.

2. **Login Credentials**  
    - Username: `admin`  
    - Password: `tryhackme`

---

## Sending Profiles

Sending profiles contain the connection details required to send phishing emails (SMTP server).  

1. Click **Sending Profiles** in the left-hand menu.  
2. Click the **New Profile** button.  
3. Fill in the following details:  
    - **Name**: Local Server  
    - **From**: `noreply@redteam.thm`  
    - **Host**: `127.0.0.1:25`  
4. Click **Save Profile**.

---

## Landing Pages

Landing pages are the websites where phishing emails direct victims.  

1. Click **Landing Pages** in the left-hand menu.  
2. Click the **New Page** button.  
3. Set the following:  
    - **Name**: ACME Login  
    - In the HTML box, click the **Source** button and paste the following code:

```html
<!DOCTYPE html>
<html lang="en">
<head>
     <meta charset="UTF-8">
     <title>ACME IT SUPPORT - Admin Panel</title>
     <style>
          body { font-family: "Ubuntu", monospace; text-align: center }
          div.login-form { margin:auto; width:300px; border:1px solid #ececec; padding:10px;text-align: left;font-size:13px;}
          div.login-form div input { margin-bottom:7px;}
          div.login-form input { width:280px;}
          div.login-form div:last-child { text-align: center; }
          div.login-form div:last-child input { width:100px;}
     </style>
</head>
<body>
     <h2>ACME IT SUPPORT</h2>
     <h3>Admin Panel</h3>
     <form method="post">
          <div class="login-form">
                <div>Username:</div>
                <div><input name="username"></div>
                <div>Password:</div>
                <div><input type="password" name="password"></div>
                <div><input type="submit" value="Login"></div>
          </div>
     </form>
</body>
</html>
```

4. Click the **Source** button again to preview the login box.  
5. Check the **Capture Submitted Data** and **Capture Passwords** boxes.  
6. Click **Save Page**.

---

## Email Templates

Email templates define the content of phishing emails.  

1. Click **Email Templates** in the left-hand menu.  
2. Click the **New Template** button.  
3. Set the following:  
    - **Name**: Email 1  
    - **Subject**: New Message Received  
    - Click the **HTML** tab, then the **Source** button.  
4. Write a persuasive email with a link to your landing page:  
    - Link text: `https://admin.acmeitsupport.thm`  
    - Actual link: `{{.URL}}` (use the link button to set this).  
5. Click **Save Template**.

---

## Users & Groups

Store the email addresses of your targets.  

1. Click **Users & Groups** in the left-hand menu.  
2. Click the **New Group** button.  
3. Set the following:  
    - **Name**: Targets  
    - Add the following email addresses:  
      - `martin@acmeitsupport.thm`  
      - `brian@acmeitsupport.thm`  
      - `accounts@acmeitsupport.thm`  
4. Click **Save Template**.

---

## Campaigns

Send your phishing emails.  

1. Click **Campaigns** in the left-hand menu.  
2. Click the **New Campaign** button.  
3. Set the following:  
    - **Name**: Campaign One  
    - **Email Template**: Email 1  
    - **Landing Page**: ACME Login  
    - **URL**: `http://MACHINE_IP`  
    - **Launch Date**: Set to 2 days ago (for this lab).  
    - **Sending Profile**: Local Server  
    - **Groups**: Targets  
4. Click **Launch Campaign** and confirm.

---

## Results

The results page shows the performance of your phishing campaign:  

- Emails delivered, opened, clicked, and data submitted.  
- Expand individual rows for detailed information.  

For example:  
- Martin and Brian's emails were sent successfully.  
- The account's email resulted in an error (user unknown).  
- Brian's status changed to **Submitted Data** after a minute.  
- View Brian's submitted data to see his username and password.

