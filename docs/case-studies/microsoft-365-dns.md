
# Case Study: Integrating `toshsystems.com` with Microsoft 365

Status: In Progress

## Summary

The lab domain was already managed through Cloudflare, while Microsoft 365 was still using the tenant's default `toshsystems.onmicrosoft.com` namespace. This project connected the existing domain to Microsoft 365 without moving DNS management away from Cloudflare. The work covered domain ownership verification, administrator identity changes, Exchange Online mail routing, SPF, autodiscover, and DKIM.

A DKIM validation issue also required checking the published records independently. Microsoft reported that the required CNAMEs could not be found even though both records were already resolving correctly from public DNS.

## Purpose

The goal was to use `toshsystems.com` as the primary domain for the Microsoft 365 environment while keeping Cloudflare as the authoritative DNS provider.

The integration was intended to provide:

* a custom Microsoft 365 sign-in domain
* email addresses using `@toshsystems.com`
* Exchange Online inbound mail routing
* SPF sender authorization
* DKIM message authentication
* Outlook autodiscovery
* an emergency administrator independent of the custom domain
* a foundation for adding DMARC

## Design Decision

Several parts of the implementation had more than one valid approach.

| Area                | Decision                        | Alternative               | Reason                                                                                        |
| ------------------- | ------------------------------- | ------------------------- | --------------------------------------------------------------------------------------------- |
| DNS management      | Manual Cloudflare configuration | Microsoft Domain Connect  | Keeps DNS changes under direct control and avoids granting Microsoft write access to the zone |
| SPF policy          | `-all`                          | `~all`                    | Exchange Online is currently the only system authorized to send mail for the domain           |
| Break-glass account | Remain on `onmicrosoft.com`     | Move to `toshsystems.com` | Keeps emergency access independent of the custom domain                                       |
| Mail-related CNAMEs | Cloudflare DNS only             | Cloudflare proxy          | Microsoft service records need to resolve directly to Microsoft endpoints                     |

The break-glass account was intentionally excluded when the other administrator identity was moved to the custom domain.

If `toshsystems.com` or its DNS configuration experiences a problem, the emergency administrator can still sign in using Microsoft's native tenant namespace.

## Implementation

### Domain Verification

Microsoft generated a TXT record containing a unique ownership value, such as MS=msXXXXXXXX, which was added at the root of toshsystems.com in Cloudflare. Within several minutes, Microsoft detected the record and verified the domain. The TXT record is only used to prove control of the DNS zone and does not participate in normal Exchange Online mail flow after verification.


### Administrator Identity

After the domain was verified, the primary administrator was changed from michael.mcintosh@toshsystems.onmicrosoft.com to michael.mcintosh@toshsystems.com. This updated the account's user principal name rather than creating a new identity, so the existing password, MFA registration, administrative roles, and account identity remained unchanged. The break-glass administrator was intentionally left on the original onmicrosoft.com namespace.

### Exchange Online DNS

The Exchange Online records were entered manually in Cloudflare.

| Record | Name           | Value                                            | Function                                                   |
| ------ | -------------- | ------------------------------------------------ | ---------------------------------------------------------- |
| MX     | `@`            | `toshsystems-com.mail.protection.outlook.com`    | Routes inbound mail to Exchange Online                     |
| TXT    | `@`            | `v=spf1 include:spf.protection.outlook.com -all` | Authorizes Exchange Online to send mail for the domain     |
| CNAME  | `autodiscover` | `autodiscover.outlook.com`                       | Allows supported clients to locate Microsoft mail settings |

The MX record uses priority 0, while SPF terminates with -all because Exchange Online is currently the only authorized sender for the domain. The autodiscover record was also created as DNS only in Cloudflare rather than being proxied.

DKIM

DKIM configuration was started through Microsoft Defender, which generated the two selectors selector1._domainkey and selector2._domainkey. Each selector was added to Cloudflare as a DNS-only CNAME. Microsoft uses two selectors so DKIM signing keys can be rotated without interrupting verification. The generated targets used Microsoft's newer DKIM namespace containing q-v1.dkim.mail.microsoft rather than the older onmicrosoft.com target format shown in many earlier setup examples.

Challenges & Troubleshooting

After both DKIM CNAME records were published, Microsoft continued to report that the records could not be found. Instead of deleting or recreating them, public DNS was checked directly with:

dig selector1._domainkey.toshsystems.com CNAME +short
dig selector2._domainkey.toshsystems.com CNAME +short

Both queries returned the expected Microsoft destinations, confirming that the selectors existed, were publicly resolvable, were not being proxied, and were pointing to the correct CNAME targets. Since the DNS configuration was already correct, the issue was isolated to a mismatch between what public DNS was returning and what Microsoft's DKIM validator was recognizing. The Cloudflare records were therefore left unchanged, with the remaining step being to retry DKIM activation after Microsoft's validation process catches up.
