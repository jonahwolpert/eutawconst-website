# Eutaw Construction LLC — Deployment Guide

Complete instructions for taking this website live at https://www.eutawconst.com using **Netlify** (free) and **Google Domains** DNS.

Total estimated time: **45–90 minutes** (one-time setup).

---

## Before You Start — Checklist

You will need:

- [ ] A GitHub account (free — github.com/signup)
- [ ] A Netlify account (free — app.netlify.com/signup) — sign up with your GitHub account
- [ ] Access to your Google Domains account (where `eutawconst.com` is registered)
- [ ] A Formspree account for the contact + quote forms (free — formspree.io/register)
- [ ] Photos for the gallery and hero sections (see "Replace placeholder images" below)

---

## Step 1 — Put the site in a GitHub repository

1. Go to https://github.com/new
2. Repository name: `eutawconst-website`
3. Set it to **Public** or **Private** (either works)
4. Do NOT initialize with a README, .gitignore, or license
5. Click **Create repository**
6. In a terminal, from inside `/home/jwolpert/Documents/EutawConstruction/website/`:

```bash
cd /home/jwolpert/Documents/EutawConstruction/website
git init
git add .
git commit -m "Initial commit — Eutaw Construction website"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/eutawconst-website.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your actual GitHub username.

---

## Step 2 — Deploy to Netlify

1. Log in at https://app.netlify.com
2. Click **Add new site → Import an existing project**
3. Choose **GitHub**, authorize Netlify, and select the `eutawconst-website` repo
4. Build settings:
   - **Build command:** *(leave blank)*
   - **Publish directory:** *(leave blank — defaults to repo root)*
5. Click **Deploy site**

Netlify will publish within ~30 seconds and give you a random URL like `https://clever-name-12345.netlify.app`. Confirm the site loads.

---

## Step 3 — Point eutawconst.com at Netlify

### 3a. Add custom domain in Netlify

1. In your Netlify site dashboard: **Site configuration → Domain management → Add a domain**
2. Enter `eutawconst.com`
3. Also add `www.eutawconst.com` (Netlify will set it as the primary)
4. Netlify shows you the DNS records you need to set.

### 3b. Update DNS in Google Domains

**Note:** Google Domains has been migrated to **Squarespace Domains**. Log in at https://account.squarespace.com/domains — your `eutawconst.com` registration is there.

1. Open the `eutawconst.com` domain → **DNS settings**
2. Remove existing A/CNAME records that point to the old Squarespace website
3. Add these records:

| Type  | Host (Name) | Value                          | TTL   |
|-------|-------------|--------------------------------|-------|
| A     | @           | `75.2.60.5`                    | 3600  |
| CNAME | www         | `YOUR-SITE-NAME.netlify.app`  | 3600  |

Replace `YOUR-SITE-NAME.netlify.app` with the Netlify URL from Step 2.

*(If Netlify shows different IPs for the A record, use whatever Netlify tells you — those values are authoritative.)*

4. Save. DNS propagation usually takes **15 minutes to 2 hours**, occasionally up to 24 hours.

### 3c. Enable HTTPS

Once DNS resolves, back in Netlify:
**Domain management → HTTPS → Verify DNS configuration → Provision certificate**

Netlify auto-issues a free Let's Encrypt cert within a few minutes.

Enable **Force HTTPS** once the cert is live.

---

## Step 4 — Wire up the contact + quote forms

The forms in `contact.html` and `quote.html` currently point to:

```
action="https://formspree.io/f/REPLACE_WITH_FORMSPREE_ID"
```

To wire them up:

1. Sign up at https://formspree.io (free — 50 submissions/month, unlimited on $10/mo plan)
2. Create a new form — set the destination email to `jonahwolpert@eutawconst.com`
3. Formspree will give you an endpoint like `https://formspree.io/f/xyzabc123`
4. Replace `REPLACE_WITH_FORMSPREE_ID` in both `contact.html` and `quote.html` with your form ID (e.g., `xyzabc123`)
5. Commit and push — Netlify will redeploy automatically

**Alternative:** Netlify has built-in forms. To use them instead, replace the `action=...` attribute with `data-netlify="true"` and add a hidden input. See https://docs.netlify.com/forms/setup/.

---

## Step 5 — Replace placeholder images

Every page has `gallery-placeholder` blocks that reference filenames like `/images/gallery/dock-01.jpg`. Replace them:

1. Create the physical images (JPEG or WebP, under 400KB each recommended)
2. Place them at `/home/jwolpert/Documents/EutawConstruction/website/images/` (and `images/gallery/`, `images/blog/` as referenced)
3. In each HTML file, replace the placeholder `<div class="gallery-placeholder">[ ... ]</div>` with real `<img>` tags:

```html
<img src="/images/gallery/dock-01.jpg"
     alt="Custom residential dock built by Eutaw Construction on Lake Marion SC"
     loading="lazy"
     width="800" height="600">
```

**Priority images to replace first:**
- `/images/hero-home.jpg` — homepage hero (and uncomment the `hero-bg-image` line in index.html)
- `/images/og-home.jpg`, `og-lake-marion.jpg`, etc. — social share previews (1200×630px)
- `/images/favicon.svg` — site icon
- `/images/logo.png` — referenced in schema
- Gallery images under `/images/gallery/`

---

## Step 6 — Post-launch SEO setup

### 6a. Google Search Console

1. Go to https://search.google.com/search-console
2. Add `https://www.eutawconst.com` as a property
3. Verify via DNS TXT record (copy the value, add a TXT record at Google/Squarespace Domains)
4. Once verified: **Sitemaps → Add a new sitemap → `sitemap.xml`**
5. Use **URL Inspection** to request indexing for every main page

### 6b. Google Business Profile

Sign in at https://business.google.com — update using content from `/home/jwolpert/Documents/EutawConstruction/01-google-business-profile.md` (description, services, service areas, hours, photos).

### 6c. Schema validation

- https://search.google.com/test/rich-results → enter `https://www.eutawconst.com/` → should show `GeneralContractor`
- Same tool → `https://www.eutawconst.com/faq.html` → should show `FAQPage`
- Same tool → `https://www.eutawconst.com/blog/how-to-build-a-dock-on-lake-marion.html` → should show `Article`

### 6d. Bing Webmaster Tools

https://www.bing.com/webmasters — import your site from Google Search Console in one click. Submit sitemap.

### 6e. Local citations (NAP consistency)

Submit the canonical NAP to:
- Bing Places (bingplaces.com)
- Apple Maps (business.apple.com)
- Yelp
- Angi / HomeAdvisor
- BBB
- Houzz, Thumbtack, Nextdoor

Use **exactly** this NAP format everywhere:

```
Eutaw Construction LLC
305 Porcher Avenue
Eutawville, SC 29048
803-496-6074
https://www.eutawconst.com
```

---

## Step 7 — Make updates later

After the initial deploy, any change is just:

```bash
cd /home/jwolpert/Documents/EutawConstruction/website
# edit files
git add .
git commit -m "Describe the change"
git push
```

Netlify auto-deploys within 30 seconds of the push.

---

## Troubleshooting

**"Site doesn't load after DNS change"** — Wait. DNS can take up to 24 hours. Check propagation at https://dnschecker.org.

**"Forms don't send"** — Confirm the Formspree form ID is correct in both `contact.html` and `quote.html`, and that Formspree has confirmed your destination email.

**"HTTPS certificate error"** — Go to Netlify → Domain management → HTTPS → click **Renew certificate**.

**"Old Squarespace site still shows"** — Clear browser cache, try an incognito window. If DNS is pointed correctly, the old site will disappear for all visitors within the propagation window.

---

## Files in this project

```
website/
├── index.html                      Homepage
├── services.html                   Services overview
├── lake-marion.html                Lake Marion SEO landing page
├── commercial.html                 Santee Cooper / commercial page
├── about.html                      About / company story
├── gallery.html                    Project gallery
├── faq.html                        FAQ (with FAQPage schema)
├── contact.html                    Contact info + form
├── quote.html                      Free quote form
├── 404.html                        Not-found page
├── blog/
│   ├── index.html                  Blog index
│   └── how-to-build-a-dock-on-lake-marion.html   Blog post #1
├── css/styles.css                  Design system
├── js/main.js                      Header scroll, mobile nav, FAQ accordion
├── images/                         (put real images here)
├── sitemap.xml                     Search engine sitemap
├── robots.txt                      Search engine directives
├── _redirects                      Netlify redirects / canonical host
└── DEPLOY.md                       (this file)
```
