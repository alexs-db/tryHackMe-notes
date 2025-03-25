# Introduction to Phishing Attacks

## What is Social Engineering?

Before understanding phishing, it's essential to grasp the concept of social engineering. Social engineering is the psychological manipulation of people into performing actions or divulging information by exploiting weaknesses in human nature. These weaknesses can include curiosity, jealousy, greed, kindness, or a willingness to help others.

## What is Phishing?

Phishing is a form of social engineering delivered through email to trick individuals into revealing personal information, credentials, or executing malicious code on their computers. These emails often appear to come from trusted sources, such as individuals or businesses, and include content designed to tempt or deceive recipients into:

- Downloading software
- Opening attachments
- Following links to fraudulent websites

### Spear-Phishing

A specific type of phishing campaign is spear-phishing. Similar to throwing a spear at a target, spear-phishing focuses on a specific individual, business, or organization rather than casting a wide net. This targeted approach makes spear-phishing highly effective for red team engagements, as the campaigns are tailored to the target, making them harder to detect by spam filters, antivirus software, and firewalls.

Red teams may be contracted to conduct phishing assessments to evaluate a business's vulnerability to such attacks. These assessments can be standalone or part of a broader engagement to gain access to computer systems or services.

### Other Phishing Methods

Phishing can also occur through other mediums, such as:

- **Smishing**: Phishing via SMS messages
- **Vishing**: Phishing via phone calls

## Example Scenario

The following scenario illustrates how an employee could be tricked into revealing their credentials:

1. The attacker identifies the physical location of the target business.
2. The attacker researches nearby food suppliers and discovers a company called "Ultimate Cookies."
3. The attacker registers the domain name `ultimate-cookies.thm`.
4. The attacker crafts an email to the target, offering free cookies for signing up on the website. Since the victim recognizes the local company, they are more likely to trust the email.
5. The victim clicks the link in the email, visits the fake website, and registers using their email and a reused password.
6. The attacker now has the victim's email address and password, enabling access to the victim's company email account. This access could lead to exposure of private company information or serve as a launchpad for further phishing attacks against other employees.

---

Next, you'll learn about setting up the infrastructure for a red team phishing campaign.