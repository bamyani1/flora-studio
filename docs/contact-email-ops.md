# Contact Email Operations

Production contact delivery is anchored on `studiobahar.com`.

## Mailbox roles

- `ICLOUD_SMTP_USER` is the SMTP login for the underlying iCloud mailbox.
- `CONTACT_EMAIL` is the verified custom-domain sender and the inbox that receives inquiries.
- Current production expectation:
  - `ICLOUD_SMTP_USER=<your iCloud mailbox login>`
  - `CONTACT_EMAIL=info@studiobahar.com`

## DNS requirements for `studiobahar.com`

- Keep the existing iCloud MX records.
- Keep the existing SPF record: `v=spf1 include:icloud.com ~all`
- Keep the Apple-issued DKIM selector records for the custom-domain mailbox.
- Publish a DMARC TXT record:

```txt
Host: _dmarc
Type: TXT
Value: v=DMARC1; p=quarantine; adkim=s; aspf=s; rua=mailto:info@studiobahar.com
```

## Verification

Use these checks after DNS or mailbox changes:

```bash
dig +short MX studiobahar.com
dig +short TXT studiobahar.com
dig +short TXT _dmarc.studiobahar.com
dig +short TXT sig1._domainkey.studiobahar.com
```

The contact form is production-ready only when:

- the public page displays `info@studiobahar.com`
- the inquiry email arrives in the studio inbox
- the submitter receives the auto-reply
- SPF, DKIM, and DMARC all align in the delivered message headers
