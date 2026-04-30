# Session: GitHub Pages Setup for kavalalabs.com
**Date:** 2026-04-30  
**Project:** kavalalabs/kavalalabs  
**Goal:** Set up GitHub Pages landing page for Kavala Labs

---

## Summary

Set up a full GitHub Pages deployment for kavalalabs.com including landing page, CNAME, git repo initialization, push to GitHub, Pages activation, and branch protection.

---

## Responses

### 1 — Initial setup plan

I'll set up the GitHub Pages landing page for Kavala Labs. Checking the workspace folder and creating the necessary files.

- Workspace folder existed but was empty.
- Created `index.html` — full landing page with: hero, pillars (6 cards), about section with stats, products section (4 cards), email signup, footer.
- Created `CNAME` file containing `kavalalabs.com`.
- Initialized git repo, staged and committed both files on `main`.

**Commit message:** `Initial GitHub Pages setup for kavalalabs.com`

---

### 2 — Push attempt & auth issue

Attempted `git push -u origin main` — failed with "Repository not found" because the active GitHub account (`vinaykavala`) didn't have access to the `kavalalabs` org.

Checked `gh auth status` — found two accounts: `vinaykavala` (active) and `socialkavala` (inactive).

Repo is under a third account (`teja@kavalalabs.com`). Stopped the `gh auth login` background process after user confirmed they'd log in from the terminal directly.

**Handoff to user:**
```bash
gh auth login
gh auth switch --user <kavalalabs-username>
cd "/Users/vkavala/Library/Mobile Documents/com~apple~CloudDocs/dev/workspace/kavalalabs"
git push -u origin main
```

DNS setup instructions provided (A records + www CNAME).

---

### 3 — After user authenticated

User authenticated `tejakavala` account. Verified access to `kavalalabs/kavalalabs` repo.

Remote had one existing commit (`README.md` only from an initial commit by tejakavala). Rebased local `main` onto `origin/main` and pushed successfully.

---

### 4 — GitHub Pages activation

Attempted to enable GitHub Pages — failed with HTTP 422: plan doesn't support Pages for private repos.

Confirmed repo was **private**. Asked user for confirmation to make it public (required for Pages on free plan). User approved.

- Made repo **public**.
- Enabled GitHub Pages: branch `main`, path `/`, custom domain `kavalalabs.com` auto-detected from CNAME.
- Pages URL: `http://kavalalabs.com/` (HTTPS pending DNS).

---

### 5 — Contributor restrictions

- Forking: org-level policy already disallows it (no change needed).
- Branch protection on `main`:
  - Only `tejakavala` can push directly.
  - PRs require 1 approving review before merge.
  - Stale reviews dismissed on new commits.
  - Force pushes and branch deletion blocked.

---

### 6 — DNS steps (detailed)

See section below.

---

## DNS Configuration — Detailed Steps

### What you need to configure

| Record Type | Host / Name | Value | TTL |
|-------------|-------------|-------|-----|
| A | `@` | `185.199.108.153` | 3600 (or Auto) |
| A | `@` | `185.199.109.153` | 3600 |
| A | `@` | `185.199.110.153` | 3600 |
| A | `@` | `185.199.111.153` | 3600 |
| CNAME | `www` | `kavalalabs.github.io` | 3600 |

---

### Steps by registrar

#### GoDaddy
1. Log in → **My Products** → find `kavalalabs.com` → **DNS**
2. Under **Records**, click **Add** for each A record:
   - Type: `A` | Name: `@` | Value: `185.199.108.153` | TTL: `1 hour`
   - Repeat for the other three IP addresses
3. Find any existing `CNAME` for `www` and edit it, or add new:
   - Type: `CNAME` | Name: `www` | Value: `kavalalabs.github.io` | TTL: `1 hour`
4. Save changes.

#### Namecheap
1. Log in → **Domain List** → `kavalalabs.com` → **Manage** → **Advanced DNS**
2. Under **Host Records**, click **Add New Record**:
   - Type: `A Record` | Host: `@` | Value: `185.199.108.153` | TTL: `Automatic`
   - Repeat for the other three IPs
3. Add or edit the `www` CNAME:
   - Type: `CNAME Record` | Host: `www` | Value: `kavalalabs.github.io` | TTL: `Automatic`
4. Click the green checkmark to save each record.

#### Cloudflare
1. Log in → select `kavalalabs.com` → **DNS** → **Records**
2. Click **Add record** for each A record:
   - Type: `A` | Name: `@` | IPv4: `185.199.108.153` | Proxy: **DNS only** (grey cloud, NOT orange)
   - Repeat for the other three IPs
3. Add the CNAME:
   - Type: `CNAME` | Name: `www` | Target: `kavalalabs.github.io` | Proxy: **DNS only**
4. Save.

> **Important for Cloudflare:** GitHub Pages HTTPS won't work if the records are proxied (orange cloud). Set them to **DNS only** (grey cloud).

#### Google Domains / Squarespace Domains
1. Log in → select `kavalalabs.com` → **DNS** → **Manage custom records**
2. Add A records:
   - Host name: *(leave blank or `@`)* | Type: `A` | TTL: `3600` | Data: `185.199.108.153`
   - Repeat for the other three IPs
3. Add CNAME:
   - Host name: `www` | Type: `CNAME` | TTL: `3600` | Data: `kavalalabs.github.io`
4. Save.

---

### After DNS is set

1. Go to: [https://github.com/kavalalabs/kavalalabs/settings/pages](https://github.com/kavalalabs/kavalalabs/settings/pages)
2. Under **Custom domain**, confirm `kavalalabs.com` is shown.
3. Wait for the DNS check to pass (green checkmark) — usually 15 min to 2 hours.
4. Once DNS is verified, check **Enforce HTTPS** to enable SSL.

### Verify from terminal
```bash
# Check A records resolved
dig kavalalabs.com +noall +answer

# Check www CNAME
dig www.kavalalabs.com +noall +answer

# Should see GitHub IPs for @ and kavalalabs.github.io for www
```

---

## Files Created This Session

| File | Purpose |
|------|---------|
| `index.html` | Landing page — Kavala Labs brand site |
| `CNAME` | Custom domain declaration for GitHub Pages |

## Repo

- GitHub: https://github.com/kavalalabs/kavalalabs
- Pages URL: https://kavalalabs.com (pending DNS)
- Branch protection: main locked to tejakavala, PRs require 1 review
