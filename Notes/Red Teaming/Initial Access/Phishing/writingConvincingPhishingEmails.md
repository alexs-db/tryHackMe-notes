# Writing Convincing Phishing Emails

When crafting phishing emails, there are three key elements to consider: the sender's email address, the subject, and the content.

## The Sender's Address

Ideally, the sender's address should spoof a significant brand, a known contact, or a coworker. For more details, refer to the "Choosing A Phishing Domain" task.

To identify brands or individuals the victim interacts with, you can use OSINT (Open Source Intelligence) techniques, such as:

- Observing their social media accounts for any brands or friends they mention.
- Searching Google for the victim's name and approximate location to find reviews they may have left about local businesses or brands.
- Checking the victim's business website for suppliers.
- Browsing LinkedIn to identify coworkers of the victim.

## The Subject

The subject line should be urgent, concerning, or intriguing to prompt the victim to act quickly. Examples include:

- "Your account has been compromised."
- "Your package has been dispatched/shipped."
- "Staff payroll information (do not forward!)"
- "Your photos have been published."

## The Content

When impersonating a brand or supplier, research their standard email templates and branding (e.g., style, logos, images, sign-offs) to make your email appear authentic. If impersonating a contact or coworker, consider reaching out to them first to gather details like:

- Branding in their email template.
- Specific email signatures.
- Personal nuances, such as how they refer to themselves (e.g., someone named Dorothy might sign off as "Dot").

These small details can have a significant psychological impact on the victim, increasing the likelihood of them engaging with the email.

If you’ve set up a spoof website to harvest data or distribute malware, disguise the links using anchor text or make them appear legitimate. For example:

```html
<a href="http://spoofsite.thm">Click Here</a>

<a href="http://spoofsite.thm">https://onlinebank.thm</a>
```
