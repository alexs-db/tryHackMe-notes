```markdown
# Phishing Infrastructure

To launch a successful phishing campaign, a certain amount of infrastructure needs to be set up.

## Domain Name
You’ll need to register either an authentic-looking domain name or one that mimics the identity of another domain. See **Task 5** for details on how to create the perfect domain name.

## SSL/TLS Certificates
Creating SSL/TLS certificates for your chosen domain name will add an extra layer of authenticity to the attack.

## Email Server/Account
You’ll need to either set up an email server or register with an SMTP email provider.

## DNS Records
Setting up DNS records such as **SPF**, **DKIM**, and **DMARC** will improve the deliverability of your emails and ensure they land in the inbox rather than the spam folder.

## Web Server
You’ll need to set up web servers or purchase web hosting from a company to host your phishing websites. Adding SSL/TLS to the websites will give them an extra layer of authenticity.

## Analytics
When a phishing campaign is part of a red team engagement, keeping analytics information is crucial. You’ll need tools to track:
- Emails sent, opened, or clicked.
- Information from phishing websites, such as users who supplied personal information or downloaded software.

## Automation and Useful Software
Some of the above infrastructure can be quickly automated using the tools below:

### GoPhish
- **Type**: Open-Source Phishing Framework  
- **Website**: [getgophish.com](https://getgophish.com)  

GoPhish is a web-based framework that simplifies setting up phishing campaigns. It allows you to:
- Store SMTP server settings for sending emails.
- Use a WYSIWYG (What You See Is What You Get) editor to create email templates.
- Schedule email delivery.
- Access an analytics dashboard to track emails sent, opened, or clicked.

The next task will guide you through launching a phishing campaign using this software.

### SET (Social Engineering Toolkit)
- **Website**: [trustedsec.com](https://www.trustedsec.com)  

The Social Engineering Toolkit includes various tools, with key features for phishing such as:
- Creating spear-phishing attacks.
- Deploying fake versions of common websites to trick victims into entering their credentials.
```
