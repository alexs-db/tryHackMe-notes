# Choosing a Phishing Domain

Choosing the right phishing domain to launch your attack from is essential to ensure you have the psychological edge over your target. A red team engagement can use some of the methods below for selecting the perfect domain name.

## Expired Domains

Although not essential, buying a domain name with some history may lead to better scoring of your domain when it comes to spam filters. Spam filters tend to distrust brand-new domain names compared to ones with some history.

## Typosquatting

Typosquatting is when a registered domain looks very similar to the target domain you're trying to impersonate. Here are some common methods:

- **Misspelling**: `goggle.com` vs `google.com`
- **Additional Period**: `go.ogle.com` vs `google.com`
- **Switching Numbers for Letters**: `g00gle.com` vs `google.com`
- **Phrasing**: `googles.com` vs `google.com`
- **Additional Word**: `googleresults.com` vs `google.com`

These changes might look unrealistic, but at a glance, the human brain tends to fill in the blanks and see what it expects, i.e., the correct domain name.

## TLD Alternatives

A TLD (Top-Level Domain) is the `.com`, `.net`, `.co.uk`, `.org`, `.gov`, etc., part of a domain name. There are hundreds of TLD variants now. A common trick for choosing a domain is to use the same name but with a different TLD. For example, register `tryhackme.co.uk` to impersonate `tryhackme.com`.

## IDN Homograph Attack/Script Spoofing

Originally, domain names were made up of Latin characters `a-z` and `0-9`. However, in 1998, IDN (Internationalized Domain Name) was implemented to support language-specific scripts or alphabets from other languages such as Arabic, Chinese, Cyrillic, Hebrew, and more. 

An issue arising from the IDN implementation is that different letters from different languages can appear identical. For example, Unicode character `U+0430` (Cyrillic small letter "a") looks identical to Unicode character `U+0061` (Latin small letter "a") used in English. This enables attackers to register a domain name that looks almost identical to another.

![Diagram showing the difference between the Latin small "a" character and a small "a" character derived from the Cyrillic alphabet.](#)