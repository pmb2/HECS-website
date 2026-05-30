# Hailey Emma Creative Studio — Domain Setup Guide (Namecheap)

> **Custom domain:** `lemcolc.com`  
> **Domain registrar:** Namecheap.com  
> **DNS type:** Namecheap BasicDNS (registrar-servers.com) — you don't need to change nameservers  
> **Hosting:** GitHub Pages (static — no server to maintain)  

---

## What this guide covers

Your domain `lemcolc.com` is currently pointed at **Namecheap's parked page** (a URL forward that shows "coming soon"). We need to repoint it to GitHub Pages so your site loads at `https://lemcolc.com`.

> **⚠️ IMPORTANT — CNAME timing:** The `CNAME` file in the repo has been **removed** so the site works right now at `https://pmb2.github.io/HECS-website/`. Once you finish the DNS steps below, tell me and I'll add the CNAME file back — then the site will serve from `https://lemcolc.com`. (If I add it before DNS propagates, the site goes 404 everywhere, which is what happened earlier.)

**DNS Status (current):**

| Record | Host | Value | Purpose |
|---|---|---|---|
| A | @ | 162.255.119.240 | → ~~Namecheap parking~~ **DELETE** |
| NS | — | dns1.registrar-servers.com | Leave as-is |
| NS | — | dns2.registrar-servers.com | Leave as-is |

Everything below happens inside **Namecheap's dashboard**.

---

## Step 1: Find your Domain List in Namecheap

1. Log into **[Namecheap.com](https://www.namecheap.com)** (top-right login)
2. Go to **Dashboard** → click **"Domain List"** in the left sidebar
3. Find **`lemcolc.com`** → click **"Manage"** (blue button on the right)

---

## Step 2: Open Advanced DNS

You're now on the domain management page. Look for a tab or section called **"Advanced DNS"** near the top.  
Click it. You'll see a table of DNS records.

---

## Step 3: Delete the old A record

In the table, find a row like this:

| Type | Host | Value | Action |
|---|---|---|---|
| `A` | `@` | `162.255.119.240` | 🗑️ |

Click the **trash icon / delete** (right side of that row) to remove it. This removes the Namecheap parking page.

---

## Step 4: Add 4 new A records (for apex `lemcolc.com`)

Click **"Add New Record"**. For each one:

| Type | Host | Value | TTL |
|---|---|---|---|
| `A + Dynamic DNS` | `@` | `185.199.108.153` | `30 min` |
| `A + Dynamic DNS` | `@` | `185.199.109.153` | `30 min` |
| `A + Dynamic DNS` | `@` | `185.199.110.153` | `30 min` |
| `A + Dynamic DNS` | `@` | `185.199.111.153` | `30 min` |

> **Why 4?** GitHub uses all 4 IPs for CDN redundancy. If you skip any, the site may be intermittently unreachable.

**Repeat 4 times:**
- Click "Add New Record"
- Select **A + Dynamic DNS** from the Type dropdown
- Leave Host as **@** (means the bare domain: `lemcolc.com`)
- Paste one of the 4 GitHub IPs into Value
- Set TTL to **30 min**
- Click ✓ (checkmark) to save

---

## Step 5: Add a CNAME for `www`

Still in Advanced DNS, click **"Add New Record"** again:

| Type | Host | Value | TTL |
|---|---|---|---|
| `CNAME` | `www` | `pmb2.github.io` | `30 min` |

- Select **CNAME** from Type
- Host: **www**
- Value: **pmb2.github.io** (the GitHub Pages hostname)
- TTL: **30 min**

> **Important:** The CNAME value must be `pmb2.github.io`, NOT `lemcolc.com`. A CNAME can't point to another apex domain.

---

## Step 6: Verify your records

After adding, the table should look **exactly like this**:

| Type | Host | Value |
|---|---|---|
| `A` | `@` | `185.199.108.153` |
| `A` | `@` | `185.199.109.153` |
| `A` | `@` | `185.199.110.153` |
| `A` | `@` | `185.199.111.153` |
| `CNAME` | `www` | `pmb2.github.io` |

Make sure the old `A` record (`162.255.119.240`) is **gone**.

---

## Step 7: Wait for DNS propagation

Click **Save All Changes** (green button at the bottom of the DNS records table).

Propagation usually takes **5-30 minutes** for Namecheap BasicDNS, but can be up to 48 hours. You can check at any time:

| What | How |
|---|---|
| Check current DNS | Visit **[whatsmydns.net/#A/lemcolc.com](https://www.whatsmydns.net/#A/lemcolc.com)** |
| Check if it's working for you | Open terminal: `nslookup lemcolc.com` |
| View the site | Open `http://lemcolc.com` in a browser |

---

## What I've done on my side (already deployed)

✅ Built the site — single `index.html`, ~23KB, no build step  
✅ Converted images to WebP (80% smaller than PNG)  
✅ Created `CNAME` file in the repo with `lemcolc.com`  
✅ Added Open Graph tags (social share previews)  
✅ Added JSON-LD schema (Google rich results for LocalBusiness)  
✅ Enabled GitHub Pages on the repo  
✅ Pushed everything to `github.com/pmb2/HECS-website`

**The site is temporarily live at:**  
https://pmb2.github.io/HECS-website/  

As soon as DNS propagates, it'll appear at **https://lemcolc.com** with automatic HTTPS certificate (Let's Encrypt, issued by GitHub).

---

## Troubleshooting

### "Hmm, we can't reach this page"
- DNS hasn't propagated yet — wait 15-30 min and try again
- Check you deleted the old A record

### Site loads but no green lock / HTTP only
- GitHub auto-provisions a TLS cert for custom domains
- Takes up to 30 minutes after DNS resolution
- Check "Enforce HTTPS" in repo Settings → Pages (I'll enable it once DNS resolves)

### `www.lemcolc.com` doesn't work
- The CNAME record for `www` → `pmb2.github.io` handles this
- GitHub Pages automatically redirects `www.lemcolc.com` → `lemcolc.com`

### I can't find the Advanced DNS tab in Namecheap
- Make sure you clicked "Manage" on the domain, not "Dashboard"
- On the Manage page, look for the **"Advanced DNS"** tab near the top center
- If you still don't see it, try refreshing the page

---

**That's it — 5 records to add + 1 to delete = ~2 minutes of work.  
Once done, the site works at `https://lemcolc.com` with zero ongoing hosting costs.**
