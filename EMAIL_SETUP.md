# Email and Contact Form Setup Guide

This guide provides step-by-step instructions for setting up the contact form on your WRT Pros Field Report website and configuring professional email for the wrtpros.com domain.

## Table of Contents
1. [Formspree Contact Form Setup](#formspree-contact-form-setup)
2. [Email Setup Options](#email-setup-options)
3. [Option A: Zoho Mail (Recommended - Full Mailbox)](#option-a-zoho-mail-recommended---full-mailbox)
4. [Option B: ImprovMX (Simple Forwarding)](#option-b-improvmx-simple-forwarding)
5. [Testing the Setup](#testing-the-setup)

---

## Formspree Contact Form Setup

Formspree is a form backend service that handles form submissions and emails them to you. It's free for up to 50 submissions per month.

### Step 1: Create a Formspree Account

1. Go to [https://formspree.io](https://formspree.io)
2. Click "Get Started" or "Sign Up"
3. Create an account using your email (edc@wrtpros.com once configured, or a temporary email initially)
4. Verify your email address

### Step 2: Create a New Form

1. After logging in, click "+ New Form" in your Formspree dashboard
2. Give your form a name (e.g., "WRT Pros Contact Form")
3. Set the email address where submissions should be sent: **edc@wrtpros.com**
4. Click "Create Form"

### Step 3: Get Your Form ID

1. After creating the form, you'll see your form endpoint URL
2. It will look like: `https://formspree.io/f/xyzabc123`
3. The form ID is the part after `/f/` (e.g., `xyzabc123`)

### Step 4: Update index.html

1. Open `index.html` in your text editor
2. Find this line:
   ```html
   <form action="https://formspree.io/f/your_form_id" method="POST">
   ```
3. Replace `your_form_id` with your actual form ID:
   ```html
   <form action="https://formspree.io/f/xyzabc123" method="POST">
   ```
4. Save the file and deploy your changes

### Optional: Customize Formspree Settings

In your Formspree dashboard, you can:
- Enable reCAPTCHA spam protection (recommended)
- Customize the "Thank You" message
- Set up email notifications
- Add webhooks for integrations

---

## Email Setup Options

You have two main options for handling email at wrtpros.com:

1. **Zoho Mail** (Recommended) - Full email hosting with mailbox, IMAP/SMTP access
2. **ImprovMX** - Simple email forwarding to an existing Gmail/Yahoo account

---

## Option A: Zoho Mail (Recommended - Full Mailbox)

Zoho Mail offers a free tier with 5GB storage and is a professional email solution.

### Step 1: Sign Up for Zoho Mail

1. Go to [https://www.zoho.com/mail/](https://www.zoho.com/mail/)
2. Click "Sign Up Now" and select the Free plan
3. Enter your domain: **wrtpros.com**
4. Create your admin account

### Step 2: Create Your Email Account

1. In Zoho Mail Control Panel, go to "User Details"
2. Click "Add User"
3. Create the email: **edc@wrtpros.com**
4. Set a strong password
5. Save the user

### Step 3: Configure DNS Records

Log in to your domain registrar or DNS provider (where you registered wrtpros.com) and add the following DNS records:

#### MX Records (Mail Exchange)
Add these MX records with the specified priorities:

| Type | Priority | Host/Name | Value/Points To        | TTL  |
|------|----------|-----------|------------------------|------|
| MX   | 10       | @         | mx.zoho.com.           | 3600 |
| MX   | 20       | @         | mx2.zoho.com.          | 3600 |
| MX   | 50       | @         | mx3.zoho.com.          | 3600 |

**Note:** Some DNS providers use `@` for the root domain, others use blank or `wrtpros.com`

#### SPF Record (Sender Policy Framework)
SPF helps prevent email spoofing:

| Type | Host/Name | Value                                      | TTL  |
|------|-----------|---------------------------------------------|------|
| TXT  | @         | `v=spf1 include:zoho.com ~all`             | 3600 |

If you already have an SPF record, modify it to include `include:zoho.com`

#### DKIM Record (DomainKeys Identified Mail)
DKIM adds a digital signature to your emails:

1. In Zoho Mail Control Panel, go to "Email Configuration" → "DKIM"
2. Zoho will provide you with a TXT record like:

| Type | Host/Name            | Value                           | TTL  |
|------|----------------------|---------------------------------|------|
| TXT  | zmail._domainkey     | [Long string provided by Zoho]  | 3600 |

Copy the exact values from Zoho's interface.

#### DMARC Record (Domain-based Message Authentication)
DMARC tells receiving servers how to handle unauthenticated emails:

| Type | Host/Name  | Value                                                              | TTL  |
|------|------------|--------------------------------------------------------------------|------|
| TXT  | _dmarc     | `v=DMARC1; p=quarantine; rua=mailto:edc@wrtpros.com; pct=100`    | 3600 |

**DMARC Policy Options:**
- `p=none` - Monitor only (recommended for testing)
- `p=quarantine` - Send suspicious emails to spam (recommended after testing)
- `p=reject` - Reject all unauthenticated emails (most strict)

Start with `p=none` for 1-2 weeks to monitor, then upgrade to `p=quarantine`.

### Step 4: Verify DNS Configuration in Zoho

1. Return to Zoho Mail Control Panel
2. Go to "Email Configuration" → "Domain Setup"
3. Zoho will verify your MX, SPF, and DKIM records
4. This may take 15 minutes to 48 hours for DNS propagation

### Step 5: Access Your Mailbox

Once verified, you can access your email at:
- **Webmail:** [https://mail.zoho.com](https://mail.zoho.com)
- **Mobile Apps:** Available for iOS and Android
- **IMAP/SMTP Settings** (for Outlook, Thunderbird, etc.):
  - **IMAP Server:** imap.zoho.com, Port 993, SSL
  - **SMTP Server:** smtp.zoho.com, Port 465, SSL
  - **Username:** edc@wrtpros.com
  - **Password:** [Your Zoho password]

---

## Option B: ImprovMX (Simple Forwarding)

ImprovMX is a free email forwarding service - emails sent to edc@wrtpros.com will forward to your personal Gmail or other email account.

**Note:** This is forwarding only - you cannot send emails FROM edc@wrtpros.com unless you configure an SMTP alias in Gmail.

### Step 1: Sign Up for ImprovMX

1. Go to [https://improvmx.com](https://improvmx.com)
2. Click "Add domain" on the homepage
3. Enter your domain: **wrtpros.com**
4. Create a free account (no credit card required)

### Step 2: Configure Email Forwarding

1. In ImprovMX dashboard, under your domain
2. Click "Add Alias"
3. Set up forwarding:
   - **Alias:** edc
   - **Forward to:** your-personal-email@gmail.com (your real email)
4. Save the alias

### Step 3: Configure DNS Records

Add the following MX records in your DNS provider:

#### MX Records

| Type | Priority | Host/Name | Value/Points To        | TTL  |
|------|----------|-----------|------------------------|------|
| MX   | 10       | @         | mx1.improvmx.com.      | 3600 |
| MX   | 20       | @         | mx2.improvmx.com.      | 3600 |

#### SPF Record

| Type | Host/Name | Value                                        | TTL  |
|------|-----------|----------------------------------------------|------|
| TXT  | @         | `v=spf1 include:spf.improvmx.com ~all`      | 3600 |

#### DKIM Record (Optional but Recommended)

1. In ImprovMX dashboard, go to your domain settings
2. Find the DKIM section
3. ImprovMX will provide a TXT record:

| Type | Host/Name        | Value                               | TTL  |
|------|------------------|-------------------------------------|------|
| TXT  | im._domainkey    | [String provided by ImprovMX]       | 3600 |

#### DMARC Record

| Type | Host/Name  | Value                                                              | TTL  |
|------|------------|--------------------------------------------------------------------|------|
| TXT  | _dmarc     | `v=DMARC1; p=none; rua=mailto:your-personal-email@gmail.com`      | 3600 |

Replace `your-personal-email@gmail.com` with your personal email.

### Step 4: Verify in ImprovMX

1. Return to ImprovMX dashboard
2. ImprovMX will automatically verify your MX records
3. DNS propagation can take 15 minutes to 48 hours

### Step 5: (Optional) Send Email FROM edc@wrtpros.com via Gmail

To reply as edc@wrtpros.com from Gmail:

1. In Gmail, go to Settings → "Accounts and Import"
2. Under "Send mail as", click "Add another email address"
3. Enter: **edc@wrtpros.com**
4. Use ImprovMX's SMTP relay:
   - **SMTP Server:** smtp.improvmx.com
   - **Port:** 587 (TLS)
   - **Username:** your-personal-email@gmail.com
   - **Password:** [ImprovMX API key from dashboard]

---

## Testing the Setup

### Test 1: Verify DNS Propagation

Use online tools to verify your DNS records are properly configured:

1. **MX Records:** [https://mxtoolbox.com](https://mxtoolbox.com)
   - Enter: `wrtpros.com`
   - Verify MX records point to correct mail servers

2. **SPF Record:** [https://mxtoolbox.com/spf.aspx](https://mxtoolbox.com/spf.aspx)
   - Enter: `wrtpros.com`
   - Should show your SPF record as valid

3. **DMARC Record:** [https://mxtoolbox.com/dmarc.aspx](https://mxtoolbox.com/dmarc.aspx)
   - Enter: `wrtpros.com`
   - Should show your DMARC policy

### Test 2: Send a Test Email

1. Send an email from your personal email to **edc@wrtpros.com**
2. Wait a few minutes (first emails may be slower)
3. Verify you receive it in:
   - Zoho Mail webmail (if using Zoho)
   - Your forwarding address (if using ImprovMX)

### Test 3: Test the Contact Form

1. Deploy your updated `index.html` to your web hosting
2. Visit your website and go to the Contact section
3. Fill out the form with test data
4. Submit the form
5. Check edc@wrtpros.com for the submission

**Expected behavior:**
- Formspree will send a confirmation email to verify the form is working
- You should receive the test submission at edc@wrtpros.com

### Test 4: Email Deliverability Test

Send a test email FROM edc@wrtpros.com to:
- A Gmail account
- An Outlook/Hotmail account
- Use [https://www.mail-tester.com](https://www.mail-tester.com) to check your email reputation score

**Good score:** 8/10 or higher means your SPF/DKIM/DMARC are properly configured.

---

## Troubleshooting

### Contact form submissions not arriving

1. Check that you updated the Formspree form ID in `index.html`
2. Verify in Formspree dashboard that the form is active
3. Check spam folder in your email
4. Verify email address in Formspree matches your actual email

### Emails not being received

1. Use MXToolbox to verify DNS records are properly set
2. Allow 24-48 hours for DNS propagation
3. Check spam/junk folder
4. Verify MX records have correct priority values

### SPF/DKIM/DMARC failures

1. Use MXToolbox to validate each record
2. Ensure no typos in TXT record values
3. Don't include quotes unless your DNS provider requires them
4. Wait for DNS propagation (can take up to 48 hours)

### Cannot send emails (Zoho)

1. Verify you're using the correct SMTP settings
2. Enable "Less secure app access" if using older email clients
3. Try the Zoho mobile app or webmail first to confirm account works

---

## Summary Checklist

- [ ] Created Formspree account and form
- [ ] Updated `index.html` with actual Formspree form ID
- [ ] Chose email solution (Zoho Mail or ImprovMX)
- [ ] Added MX records to DNS
- [ ] Added SPF record to DNS
- [ ] Added DKIM record to DNS
- [ ] Added DMARC record to DNS
- [ ] Waited for DNS propagation (up to 48 hours)
- [ ] Verified DNS records using MXToolbox
- [ ] Sent test email to edc@wrtpros.com
- [ ] Tested contact form submission
- [ ] Checked email deliverability score
- [ ] Deployed updated website

---

## Additional Resources

- **Formspree Documentation:** [https://help.formspree.io](https://help.formspree.io)
- **Zoho Mail Help:** [https://www.zoho.com/mail/help/](https://www.zoho.com/mail/help/)
- **ImprovMX Help:** [https://improvmx.com/guides/](https://improvmx.com/guides/)
- **MXToolbox (DNS Testing):** [https://mxtoolbox.com](https://mxtoolbox.com)
- **SPF Record Testing:** [https://www.kitterman.com/spf/validate.html](https://www.kitterman.com/spf/validate.html)

---

**Need Help?** If you encounter issues during setup, most DNS and email delivery problems are solved by:
1. Waiting for full DNS propagation (24-48 hours)
2. Verifying no typos in DNS records
3. Checking that records are added to the correct domain
4. Using MXToolbox to validate each record type
