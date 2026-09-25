# Deploying to GitHub Pages with larakarki.com

The site builds with GitHub Actions ([.github/workflows/pages.yml](.github/workflows/pages.yml)), not GitHub Pages' built-in Jekyll, because it uses Jekyll 4 and Tailwind. Every push to `main` rebuilds and redeploys the site.

Who does what for larakarki.com:

| Role | Company | What you do there |
|---|---|---|
| Domain registration and renewal | **WebzPro** (reseller for Realtime Register) | Renew the domain. It expires **2026-11-02**, so keep auto-renew on. Don't change the nameservers. |
| DNS and email | **May First** (mayfirst.org) | Change the website records (step 6). Leave the email records alone. |

WebzPro's "manage nameservers" page may show an empty field even though the registry has `a/b/c.ns.mayfirst.org` set. Don't save that form while it's blank.

Replace `<username>` below with your GitHub username.

## 1. Before you switch

- **Back up WordPress.** Export it (WordPress admin → Tools → Export) and download anything in `wp-content/uploads` you want to keep. After the DNS change, larakarki.com stops pointing at WordPress.
- The CV is already copied into this site at `assets/files/2026-CV-LKarki.pdf`. When you update your CV, replace that file and update the `nav` link in `_config.yml` if the filename changes.

## 2. Put the code on GitHub

1. On github.com, create a new **public** repository named `larakarki`. Don't add a README, .gitignore, or license. (Pages on a private repo requires a paid plan.)
2. In the project folder:

   ```sh
   git init -b main
   git add .
   git commit -m "Initial Jekyll site"
   git remote add origin https://github.com/<username>/larakarki.git
   git push -u origin main
   ```

## 3. Turn on GitHub Pages

1. In the repo: **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **GitHub Actions**.
3. Open the **Actions** tab. The "Deploy to GitHub Pages" run should start (if not, click it → **Run workflow**). It takes about 1–2 minutes.
4. When it's green, check the site at `https://<username>.github.io/larakarki/`.

## 4. Verify the domain with GitHub (recommended)

Verifying stops anyone else from claiming larakarki.com on GitHub Pages.

1. Your **profile** Settings (not the repo) → **Pages** → **Add a domain** → enter `larakarki.com`.
2. GitHub shows a TXT record, named something like `_github-pages-challenge-<username>`, and a value.
3. Add that TXT record in the May First DNS settings (step 6 explains where), then click **Verify**. It can take a few minutes to go through.

## 5. Tell GitHub about the custom domain

Do this **before** changing DNS.

1. Repo **Settings → Pages → Custom domain** → enter `larakarki.com` → **Save**.
2. The DNS check will fail until step 6 is done. That's expected.

## 6. Point the domain at GitHub (May First)

Log in to the May First members control panel (members.mayfirst.org), open your membership, and find the DNS records for `larakarki.com`. If you can't find where DNS is managed, May First support can make these changes for you. Send them this list.

**Change these records:**

| Host | Type | Value |
|---|---|---|
| `larakarki.com` (blank or `@`) | A | `185.199.108.153` |
| `larakarki.com` | A | `185.199.109.153` |
| `larakarki.com` | A | `185.199.110.153` |
| `larakarki.com` | A | `185.199.111.153` |
| `www` | CNAME | `<username>.github.io` |

Optional, for IPv6:

| Host | Type | Value |
|---|---|---|
| `larakarki.com` | AAAA | `2606:50c0:8000::153` |
| `larakarki.com` | AAAA | `2606:50c0:8001::153` |
| `larakarki.com` | AAAA | `2606:50c0:8002::153` |
| `larakarki.com` | AAAA | `2606:50c0:8003::153` |

**Delete:** the existing A record for `larakarki.com` and the existing A record for `www`. Both currently point to `204.19.241.95`, the WordPress server. A `www` CNAME can't sit next to other `www` records.

**Leave alone:** the MX records (`a/b/c.mx.mayfirst.org`) and any email TXT records (SPF, DKIM, DMARC). Changing them would break your email.

If May First's web hosting for the domain is still switched on, their system may re-add the old A records. In that case, ask support to switch off the web hosting for larakarki.com but keep DNS and email.

## 7. Wait, then turn on HTTPS

1. DNS changes usually show up within an hour, but they can take up to 24 hours. To check from a terminal:

   ```sh
   dig +short larakarki.com       # should list the four 185.199.x.153 addresses
   dig +short www.larakarki.com   # should show <username>.github.io
   ```

2. In repo **Settings → Pages**, the custom domain check turns green.
3. Tick **Enforce HTTPS**. If it's greyed out, GitHub is still issuing the certificate. Wait up to an hour and reload.
4. Visit https://larakarki.com and https://www.larakarki.com. Both should load the new site.

## Updating the site later

Edit, then commit and push:

```sh
git add .
git commit -m "Describe the change"
git push
```

The Actions workflow rebuilds the CSS and the site and deploys it in about 2 minutes.

## Troubleshooting

- **The Actions run fails at `bundle install`:** run `bundle lock --add-platform x86_64-linux` locally, then commit and push `Gemfile.lock`.
- **The site loads without styles at `<username>.github.io/larakarki/`:** that URL uses a path prefix. It fixes itself once the custom domain is active. All asset links use `relative_url`, so they should work either way.
- **"Domain's DNS record could not be retrieved"** in Settings → Pages: DNS hasn't finished updating yet. Wait and click **Check again**.
- **Custom domain resets after a deploy:** this shouldn't happen with the Actions workflow, because the domain is stored in repo settings. If it does, re-enter it in Settings → Pages.
