# Nova Swing — Landing Page

A single-page, self-contained landing site for Nova Swing, India's AI golf
coaching platform. Built as one HTML file plus a small runtime — no build step,
no framework install, no server. It can be hosted on any static host; these
instructions use **GitHub Pages** + **Zoho Mail** on a custom domain.

---

## 1. File structure

### What's in the project now

```
.
├── uploads/
│   ├── Nova Swing.dc.html   ← the page you edit (markup + logic + styles)
│   └── support.js           ← runtime that the page loads via ./support.js
└── README.md
```

`Nova Swing.dc.html` is the source of truth. Everything (HTML, CSS, the
JavaScript for the waitlist forms and scroll animation) lives inside it.
`support.js` is generated automatically and must always sit **next to** the
HTML file — the page loads it with a relative path (`<script src="./support.js">`).

### What you deploy to GitHub Pages

GitHub Pages serves `index.html` by default, and **filenames with spaces break
in URLs**. So the deployed repo should look like this (flat, at the repo root):

```
.
├── index.html      ← a copy of "Nova Swing.dc.html", renamed (no spaces)
├── support.js      ← copied as-is, alongside index.html
├── grass.svg       ← the grass graphic referenced near the bottom of the page
├── CNAME           ← your custom domain (one line, e.g. nova-swing.com)
└── README.md
```

> **Keep editing `Nova Swing.dc.html`.** When you're ready to publish, just copy
> it to `index.html`. Don't hand-edit `index.html` and the `.dc.html` separately
> or they'll drift apart.

---

## 2. Files you need to modify / add

| File | Action | Why |
|------|--------|-----|
| `index.html` | **Create** (copy of `Nova Swing.dc.html`) | GitHub Pages' default entry page; no spaces in the name. |
| `support.js` | **Copy as-is** | The page won't run without it. Keep it next to `index.html`. |
| `grass.svg` | **Add** | The page references `url('grass.svg')` for the grass strip at the bottom. Until this file exists, that area is blank. (Ask and one can be generated, or drop in your own.) |
| `CNAME` | **Create** | Tells GitHub Pages your custom domain. Contains exactly one line: your bare domain, e.g. `nova-swing.com`. |

Inside the page, the only content you'd typically change:

- **Contact emails** — search the file for `nova-swing.com` to update
  `hello@`, `partnerships@`, and `careers@` addresses.
- **Stats / copy** — headline, testimonials, and the founding-spots numbers
  are plain text in the markup.
- **Spots claimed / total** — these are tweakable values (`spotsClaimed`,
  `spotsTotal`) defined near the bottom of the file.

> **Note on the forms:** the waitlist and CTA forms currently *validate and show
> a confirmation toast only* — they do **not** send the email anywhere. To
> actually capture signups you'll need to wire the submit handlers to a form
> backend (e.g. Formspree, Google Forms, or a Zoho Forms endpoint). Ask if you'd
> like this added.

---

## 3. Publish on GitHub Pages

### A. Create the repo

1. On GitHub, click **New repository**. Name it (e.g. `nova-swing`),
   set it **Public**, and create it.
2. On your computer (or via GitHub's web "Add file → Upload files"), add the
   deploy files from the table above: `index.html`, `support.js`, `grass.svg`,
   `CNAME`, `README.md`.

   Using git locally:
   ```bash
   git init
   git add index.html support.js grass.svg CNAME README.md
   git commit -m "Initial Nova Swing site"
   git branch -M main
   git remote add origin https://github.com/<your-username>/nova-swing.git
   git push -u origin main
   ```

### B. Turn on Pages

1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Set **Branch** to `main` and folder to `/ (root)`. Click **Save**.
4. Wait ~1 minute. Your site goes live at
   `https://<your-username>.github.io/nova-swing/`.

### C. Point your custom domain at it

1. In **Settings → Pages → Custom domain**, enter your domain
   (e.g. `nova-swing.com`) and Save. This creates/updates the `CNAME` file.
2. At your **domain registrar / DNS provider**, add these records so the domain
   resolves to GitHub:

   **Apex domain** (`nova-swing.com`) — four `A` records:
   ```
   A   @   185.199.108.153
   A   @   185.199.109.153
   A   @   185.199.110.153
   A   @   185.199.111.153
   ```
   (Optionally add the matching `AAAA` records for IPv6:
   `2606:50c0:8000::153`, `:8001::153`, `:8002::153`, `:8003::153`.)

   **`www` subdomain** — one `CNAME`:
   ```
   CNAME   www   <your-username>.github.io.
   ```
3. Back in **Settings → Pages**, tick **Enforce HTTPS** once the certificate is
   issued (can take up to an hour after DNS propagates).

---

## 4. Connect Zoho Mail on the same domain

Email (MX records) and the website (A/CNAME records) are **separate DNS records
and do not conflict** — you can run GitHub Pages and Zoho Mail on the same
domain at once. Do this at the **same DNS provider** where you added the records
above.

> The exact Zoho hostnames depend on your Zoho **data center** (`.com`, `.in`,
> `.eu`, etc.). Since Nova Swing is India-based you may be on the India DC.
> **Always copy the exact values shown in your Zoho Mail admin console** —
> the records below are the typical `.com`/`.in` set as a guide.

### A. Add & verify the domain in Zoho

1. Sign in to **Zoho Mail Admin Console** (`mailadmin.zoho.com` /
   `mailadmin.zoho.in`).
2. **Domains → Add Domain**, enter `nova-swing.com`.
3. Zoho gives you a **verification record** — usually a `TXT` (or a `CNAME`).
   Add it at your DNS provider, then click **Verify** in Zoho.

### B. Add the MX records (so mail is delivered to Zoho)

Typical values — **confirm in your console**:

```
MX   @   mx.zoho.com    priority 10
MX   @   mx2.zoho.com   priority 20
MX   @   mx3.zoho.com   priority 50
```
(India DC users often see `mx.zoho.in`, `mx2.zoho.in`, `mx3.zoho.in`.)

Remove any old/placeholder MX records the registrar added by default.

### C. Add SPF and DKIM (so mail isn't marked as spam)

- **SPF** — one `TXT` record on `@`:
  ```
  TXT   @   v=spf1 include:zoho.com ~all
  ```
  (`include:zoho.in` on the India DC.)
- **DKIM** — Zoho generates a selector + key in **Email Configuration → DKIM**.
  Add the `TXT` record it gives you (host looks like `zmail._domainkey`), then
  click **Verify** in Zoho.

### D. Create the mailboxes used on the site

In Zoho, create users / aliases for the addresses the page links to:

- `hello@nova-swing.com`
- `partnerships@nova-swing.com`
- `careers@nova-swing.com`

Once MX has propagated (minutes to a few hours), send a test email to each to
confirm delivery.

---

## Quick checklist

- [ ] Copy `Nova Swing.dc.html` → `index.html` (no spaces in the name)
- [ ] Keep `support.js` beside it
- [ ] Add `grass.svg`
- [ ] Push to a public GitHub repo
- [ ] Settings → Pages → Deploy from `main` / root
- [ ] Add custom domain + `A`/`CNAME` DNS records, enforce HTTPS
- [ ] Verify domain in Zoho, add `MX` + `SPF` + `DKIM`, create mailboxes
- [ ] (Optional) wire the signup forms to a real backend
