# Project 01: Domain, DNS & Account Security for a Small Business

**Environment:** Real production environment, Cade's Detailing (my mobile auto detailing business)
**Tools:** Cloudflare Registrar & DNS, GitHub Pages, Google accounts, authenticator app MFA
**Date:** November 2026

---

## Goal

Move my business website from a default `github.io` address to a professional custom domain (`cadesdetailing.com`), and set up the accounts behind it the way I would for a small business client: separated, protected with MFA, and resistant to takeover.

## Starting point

- Business website hosted on GitHub Pages at a `github.io` project URL
- Business cards and a sponsor sign already printed with a QR code pointing to that URL
- No custom domain, no separation between personal and business accounts

## What I did

### 1. Created a dedicated admin account
- Set up a separate Google account used **only** to own and recover critical business accounts (domain registrar, future email admin)
- Enabled 2-step verification with an authenticator app, saved backup codes offline, and set personal recovery options
- Kept this address private: it does not appear on cards, the website, or social media

### 2. Registered the domain
- Registered `cadesdetailing.com` through Cloudflare Registrar using the admin account
- Confirmed WHOIS redaction (personal name, address, and phone hidden from public lookup)
- Confirmed auto-renew and the registrar transfer lock are on (`client transfer prohibited` in WHOIS)
- Completed the registrant contact verification

<img width="817" height="871" alt="Screenshot 2026-11-01 134300" src="https://github.com/user-attachments/assets/7f9d1d85-0aeb-4ec4-abc4-393df7cb773f" />


### 3. Verified domain ownership in GitHub
- Added a GitHub-provided **TXT record** in Cloudflare DNS and verified the domain at the account level
- This prevents someone else from attaching my domain to their own GitHub Pages site if my site configuration is ever removed (domain takeover protection)

<img width="954" height="117" alt="Screenshot 2026-11-01 134048" src="https://github.com/user-attachments/assets/8980f9b9-9c87-4799-8f4e-4102f414e1eb" />


### 4. Configured DNS
| Type | Name | Points to | Proxy |
|---|---|---|---|
| A (x4) | `@` | GitHub Pages IP addresses | DNS only |
| CNAME | `www` | `<username>.github.io` | DNS only |
| TXT | `_github-pages-challenge-<username>` | Verification code | DNS only |

<img width="1607" height="344" alt="Cloudflare DNS" src="https://github.com/user-attachments/assets/e5252157-8488-4aa7-b5cf-91a2a2984adf" />


### 5. Connected the domain and enforced HTTPS
- Set the custom domain on the repository's Pages settings and passed the DNS check
- Enabled **Enforce HTTPS** after GitHub issued the TLS certificate
- Verified the old `github.io` URL now redirects to `https://cadesdetailing.com`, so the printed QR codes still work

<img width="873" height="222" alt="Screenshot 2026-11-01 134328" src="https://github.com/user-attachments/assets/b6c29144-8b2a-46a0-9419-affd559aac16" />

<img width="332" height="191" alt="Screenshot 2026-11-01 134434" src="https://github.com/user-attachments/assets/a4016456-d05b-4801-9571-f75d80d97099" />


## Security decisions and why

**Separate admin account instead of personal email**
Keeps the "keys" to the business isolated from my personal inbox, which receives far more phishing and is used on many more sites.

**Registrar account does NOT use an email on the domain it controls**
If the domain ever broke (expired, misconfigured, or hijacked), email on that domain would stop working too, and I couldn't receive the password reset needed to fix it. Avoiding this circular dependency prevents a full lockout.

**DNS only instead of Cloudflare proxy**
Cloudflare recommends proxying records for DDoS protection and caching. I deliberately left them unproxied because proxying can block GitHub from validating the domain and issuing the HTTPS certificate. GitHub Pages already serves the site through its own CDN, so for a static site the trade-off favored a working certificate. Lesson: a default security recommendation doesn't always fit the architecture.

**Domain verification at the account level**
Adds protection against subdomain/domain takeover, a common real-world attack on sites hosted on shared platforms.

**WHOIS redaction**
The business runs out of a family home, so keeping the registrant address and phone number out of public records was a privacy requirement, not just a preference.

## Problems I ran into

- **Account settings vs. repository settings:** GitHub has a Pages page in both places. Domain verification lives in account settings; the custom domain lives in the repository settings. I initially looked for the custom domain field in the wrong one.
- **Cloudflare's proxy warning:** Cloudflare flags unproxied records as missing protection. I had to understand *why* the warning didn't apply here instead of following it.
- **Protecting existing printed materials:** Before changing anything, I confirmed that the QR codes on already-printed cards would keep working through GitHub's automatic redirect.

## Result

- `https://cadesdetailing.com` and `www.cadesdetailing.com` both load with a valid HTTPS certificate
- Old URL redirects automatically; no reprinting needed
- Domain is verified against takeover, registrant info is private, and the controlling account is isolated and MFA-protected

## Next steps

- Set up email forwarding for a business address and publish SPF and **DMARC** records to prevent others from spoofing email from the domain
- Audit and enable MFA on the remaining business accounts (social media, phone number, payment app)
- Back up business files and test a restore

## Skills demonstrated

DNS (A, CNAME, TXT records) · Domain registration & WHOIS privacy · TLS/HTTPS · MFA & account recovery planning · Account separation / least privilege · Domain takeover prevention · Documenting configuration changes
