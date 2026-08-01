# Denora — Setup Guide (for you, not your friend)

Total cost: **free**, except a domain name (~₹800–1,500/year) if you want one.
Expected setup time: **45–90 minutes**, mostly waiting on account signups.

---

## Overview of the pieces

| Piece | What it does | Cost |
|---|---|---|
| **GitHub** | Stores the website files | Free |
| **Netlify** (or Cloudflare Pages) | Hosts the live website | Free |
| **Sveltia CMS** | The admin panel your friend logs into | Free |
| **Cloudflare Workers** | Handles the "Sign in with GitHub" step | Free |
| Domain name | e.g. denoracollections.com | Optional, ~₹1,000/yr |

---

## Step 1 — Put the site on GitHub

1. Create a free account at [github.com](https://github.com) (use **your friend's email** if they'll eventually own it — see Step 6).
2. Create a **new repository**. Name it something like `denora-website`.
   - Set it to **Public** (private works too, but public is simpler on free tiers).
3. Upload the contents of this folder (`index.html`, `admin/`, `assets/`, `data/`) into the repo.
   - Easiest way without git: on the repo page, click **Add file → Upload files**, then drag everything in.
   - Make sure the folder structure is preserved — `admin/config.yml` must sit inside an `admin` folder.

---

## Step 2 — Point the config at your repo

Open `admin/config.yml` and edit line 8:

```yaml
repo: YOUR-GITHUB-USERNAME/YOUR-REPO-NAME
```

Change it to your actual username and repo, e.g.:

```yaml
repo: julna-denora/denora-website
```

Also confirm the `branch:` matches your repo's default branch (usually `main`).

---

## Step 3 — Deploy to Netlify

1. Sign up at [netlify.com](https://netlify.com) (free, sign in with GitHub).
2. **Add new site → Import an existing project → GitHub**, pick your repo.
3. Leave build settings **empty** — there's no build step. Publish directory: `/` (root).
4. Deploy. You'll get a URL like `denora-abc123.netlify.app`.

Your site is now live. Test it before continuing.

> Cloudflare Pages works identically if you'd rather use that.

---

## Step 4 — Set up the login for the admin panel

The CMS needs a way to log your friend in via GitHub. This uses a free Cloudflare Worker.

1. Sign up at [cloudflare.com](https://cloudflare.com) (free).
2. Follow the setup instructions for **sveltia-cms-auth**: https://github.com/sveltia/sveltia-cms-auth
   - In short: create a **GitHub OAuth App** (GitHub → Settings → Developer settings → OAuth Apps → New), deploy the Worker, and paste the OAuth Client ID and Secret into the Worker's environment variables.
3. Once deployed, you'll have a Worker URL like `https://denora-auth.yourname.workers.dev`.
4. Add it to `admin/config.yml` under the `backend:` block:

```yaml
backend:
  name: github
  repo: your-username/denora-website
  branch: main
  base_url: https://denora-auth.yourname.workers.dev
```

5. Commit that change. Netlify redeploys automatically.

**This step is the fiddliest part of the whole setup.** Budget 30 minutes and follow the sveltia-cms-auth README closely — the callback URL in your GitHub OAuth App must exactly match the Worker URL.

---

## Step 5 — Test the admin panel

1. Go to `your-site.netlify.app/admin`
2. Click **Sign in with GitHub**
3. Try adding a test product, then delete it
4. Confirm the change appears on the live site within ~1 minute (Netlify rebuilds on each save)

If this works, you're done with the hard part.

---

## Step 6 — Hand it over

1. **Your friend needs a GitHub account.** Have them create one (free, just email + password).
2. In your repo: **Settings → Collaborators → Add people** → add their GitHub username, give **Write** access.
3. They accept the email invite.
4. They can now log into `/admin` with their own GitHub account.
5. Give them `HANDOVER.md` (the plain-language guide) and do one short screen-share walkthrough.

### Transferring full ownership (optional but recommended)
- **GitHub:** Repo → Settings → General → Danger Zone → **Transfer ownership** to their account.
- **Netlify:** Site → Settings → General → **Transfer site** to their Netlify team.
- Then remove yourself as a collaborator once they're comfortable.

---

## Step 7 — Custom domain (optional)

1. Buy the domain (GoDaddy, Namecheap, Cloudflare Registrar — Cloudflare is usually cheapest at cost price).
2. Netlify → Site → **Domain management → Add custom domain**.
3. Follow Netlify's DNS instructions. HTTPS is automatic and free.

---

## Before going live — a checklist

- [ ] Replace the placeholder WhatsApp number in `data/site.json` (currently `919999999999`)
- [ ] Replace the placeholder email (currently `hello@denoracollections.com`)
- [ ] Add the real Instagram username
- [ ] Replace the four sample products with real ones and real photos
- [ ] Check prices are in the right currency for the friend's customers
- [ ] Test the WhatsApp "Order This" link actually opens a chat
- [ ] Open the site on a phone — most Indian traffic will be mobile

---

## Honest limitations of this setup

Worth knowing before you commit to it:

- **No payments.** Orders come in via WhatsApp/email and are settled manually. If your friend later wants real checkout, this is when Shopify becomes worth the monthly fee.
- **No inventory counts.** There's a Sold Out toggle, but nothing decrements automatically.
- **Photos need resizing.** Very large phone photos will slow the site. Tell your friend to keep images under ~1MB, or add a step where they compress at [squoosh.app](https://squoosh.app) first.
- **The login depends on GitHub.** If your friend loses access to their GitHub account, they lose admin access until it's recovered.
- **If something breaks, there's no support desk.** You're the support desk. Factor that into whether this or a paid platform is the right call long-term.
