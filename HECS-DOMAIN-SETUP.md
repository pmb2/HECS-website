# Hailey Emma Creative Studio — Domain Setup Guide

> **Custom domain:** `lemcolc.com`
> **Domain registrar:** Namecheap
> **DNS:** Namecheap BasicDNS (registrar-servers.com)
> **Hosting:** GitHub Pages (via `pmb2/HECS-website` repo)

---

## Current DNS Status

| Record | Value | Purpose |
|---|---|---|
| NS | dns1.registrar-servers.com | Namecheap BasicDNS (active) |
| NS | dns2.registrar-servers.com | Namecheap BasicDNS (active) |
| A | 162.255.119.240 | Namecheap parking page (placeholder) |

---

## Step 1: Enable GitHub Pages

**In your repo (`pmb2/HECS-website`):**

1. Go to **Settings → Pages** (or `https://github.com/pmb2/HECS-website/settings/pages`)
2. Under "Source", select **Deploy from a branch**
3. Branch: `main` → folder: `/` (root)
4. Click **Save**
5. After ~1-2 minutes, GitHub will show: _"Your site is published at `https://pmb2.github.io/HECS-website/`"_

> **Note:** The URL will be `pmb2.github.io/HECS-website` since the repo isn't named `pmb2.github.io`. We'll fix this with the custom domain — GitHub Pages will serve from `lemcolc.com` directly.

---

## Step 2: Add a CNAME file to the repo

Create a file called `CNAME` (no extension) in the repo root with one line:

```
lemcolc.com
```

Then commit and push:

```bash
echo "lemcolc.com" > CNAME
git add CNAME
git commit -m "Add CNAME for custom domain lemcolc.com"
git push origin main
```

**Alternative:** You can skip the file and enter `lemcolc.com` in the GitHub Pages settings UI under "Custom domain" — GitHub creates the CNAME file for you.

---

## Step 3: Configure DNS at Namecheap

Log into Namecheap and navigate to **Domain List → lemcolc.com → Manage → Advanced DNS**.

### Add these records:

| Type | Host | Value | TTL |
|---|---|---|---|
| **A** | **@** (apex) | `185.199.108.153` | Automatic (30 min) |
| **A** | **@** (apex) | `185.199.109.153` | Automatic |
| **A** | **@** (apex) | `185.199.110.153` | Automatic |
| **A** | **@** (apex) | `185.199.111.153` | Automatic |
| **CNAME** | **www** | `pmb2.github.io` | Automatic |

> **Why 4 A records?** GitHub Pages uses 4 IP addresses for redundancy and CDN distribution. All 4 must be added for reliability.

> **Why CNAME for www?** The `www` subdomain must CNAME to `pmb2.github.io` (the GitHub Pages host), not to `lemcolc.com`. This is a DNS requirement — CNAME records can't point to another domain's apex.

### Delete the existing A record:

Remove the old A record pointing to `162.255.119.240` (Namecheap parking).

### Final DNS table should look like:

```
A Record  @      → 185.199.108.153
A Record  @      → 185.199.109.153
A Record  @      → 185.199.110.153
A Record  @      → 185.199.111.153
CNAME     www    → pmb2.github.io
```

---

## Step 4: Enforce HTTPS in GitHub Pages

1. Go to **Settings → Pages** in your repo
2. Under "Custom domain", it should show `lemcolc.com` (green checkmark = DNS verified)
3. Check **"Enforce HTTPS"** — GitHub issues an automatic TLS certificate via Let's Encrypt
4. Wait 5-10 minutes for provisioning

---

## Step 5: Verify Propagation

| Tool | Command / URL | What to check |
|---|---|---|
| Browser | `https://lemcolc.com` | Site loads with lock icon |
| Browser | `https://www.lemcolc.com` | Redirects to `lemcolc.com` |
| DNS check | `nslookup lemcolc.com` | Returns 185.199.x.x IPs |
| GitHub Pages | Repo → Settings → Pages | Green checkmark, "Your site is published" |

DNS propagation can take **5 minutes to 48 hours** (usually <1 hour for Namecheap BasicDNS).

---

## Step 6: (Recommended) Add www → apex redirect

In your GitHub Pages settings, after setting `lemcolc.com` as the custom domain, the `www.lemcolc.com` CNAME record automatically redirects to `lemcolc.com`. GitHub Pages handles this natively — no extra config needed.

---

## Troubleshooting

### "Domain not configured" error in GitHub Pages
- DNS hasn't propagated yet — wait and re-check
- Make sure CNAME file says `lemcolc.com` (no `www`, no `https://`)
- Verify the A records point to the exact GitHub IPs above

### Site loads but no HTTPS
- Wait up to 24 hours for Let's Encrypt certificate
- Make sure "Enforce HTTPS" is checked in GitHub Pages settings

### www.lemcolc.com doesn't load
- Verify the CNAME record for `www` points to `pmb2.github.io` (not `lemcolc.com`)
- GitHub Pages only supports `www` subdomain redirects — other subdomains need manual redirect config

### lemcolc.com resolves to old IP
- Check that you deleted the `162.255.119.240` A record
- Flush DNS: `ipconfig /flushdns` (Windows) or `sudo dscacheutil -flushcache` (macOS)

---

## Verifying It's Live

Visit these URLs in order:

1. `https://pmb2.github.io/HECS-website/` — should work (pre-domain)
2. `http://lemcolc.com` — should redirect to HTTPS
3. `https://lemcolc.com` — final, with green lock
4. `https://www.lemcolc.com` — should redirect to `lemcolc.com`

---

## Git Repo Structure (final)

```
HECS-website/
├── assets/
│   ├── banner.png       (or banner.webp)
│   └── logo.png         (or logo.webp)
├── CNAME                ← "lemcolc.com"
├── index.html           ← main site
├── .gitignore
└── README.md
```

That's it — no web server, no hosting bill, no maintenance. Just push to GitHub and it's live.
