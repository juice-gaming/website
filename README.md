# website

The public website for **Merge Battles** by Juice Gaming, at <https://juice-gaming.com>.
Plain static HTML and one CSS file, served by GitHub Pages from the root of `main`.

| URL | File | What |
|---|---|---|
| `/` | `index.html` | Landing page |
| `/privacy/` | `privacy/index.html` | Privacy policy (the Play Console privacy policy URL) |
| `/delete-account/` | `delete-account/index.html` | Account deletion (the Play Console Data safety deletion URL) |
| any missing path | `404.html` | Not found |
| — | `style.css` | All styling |
| — | `CNAME` | The custom domain, `juice-gaming.com`. Do not delete it: Pages drops the domain without it |
| — | `.nojekyll` | Tells Pages to serve the files as they are, without Jekyll |

## 🔧 Editing

1. Edit the HTML. Open it in a browser straight from disk to check it, or run
   `python3 -m http.server 8000` here and open <http://localhost:8000>.
2. Commit and push to `main`. Pages republishes within a minute or two; the run shows under
   **Actions → pages-build-deployment**.

Rules, because the privacy policy makes promises about this site:

- **No JavaScript, no cookies, no analytics, no external fonts, images or scripts.** The policy's
  "This website" section says the site loads nothing from anyone else. Adding any of these makes
  it false.
- **Links between pages are relative** (`privacy/`, `../delete-account/`) so the site works both
  at `https://juice-gaming.com/` and at a project path such as `/website/`. `404.html` is the one
  exception: Pages serves it at any depth, so its links are root-absolute (`/privacy/`).
- **Changing what the policy says?** Update the **Effective date** at the top of
  `privacy/index.html`. Keep `privacy/` and `delete-account/` consistent: retention periods and
  the deletion steps appear on both.
- The deletion page must keep naming the app and developer exactly as on the Play listing:
  **Merge Battles**, developer **juice gaming**. Google Play checks this.

## 🌐 DNS (GoDaddy)

The domain's DNS is at GoDaddy (nameservers `ns51/ns52.domaincontrol.com`). In GoDaddy:
**My Products → juice-gaming.com → DNS → DNS Records**.

| Type | Name | Value | TTL |
|---|---|---|---|
| A | `@` | `185.199.108.153` | 1 hour |
| A | `@` | `185.199.109.153` | 1 hour |
| A | `@` | `185.199.110.153` | 1 hour |
| A | `@` | `185.199.111.153` | 1 hour |
| AAAA (optional, IPv6) | `@` | `2606:50c0:8000::153` | 1 hour |
| AAAA (optional) | `@` | `2606:50c0:8001::153` | 1 hour |
| AAAA (optional) | `@` | `2606:50c0:8002::153` | 1 hour |
| AAAA (optional) | `@` | `2606:50c0:8003::153` | 1 hour |
| CNAME | `www` | `juice-gaming.github.io` | 1 hour |

- **Delete the existing `A @` record** (`160.153.0.229`, GoDaddy's own parking/forwarding host)
  so only the four GitHub addresses remain. If GoDaddy **Forwarding** is turned on for the
  domain, turn it off; it re-creates its own `A` record.
- **Change the existing `www` CNAME** from `@` to `juice-gaming.github.io`. The CNAME points at
  the organisation's host, never at a repository path.
- **Do not touch the mail records**: `MX @ → juicegaming-com01c.mail.protection.outlook.com`,
  `CNAME autodiscover`, the `TXT` SPF record (`v=spf1 include:secureserver.net -all`), the
  `TXT` `NETORGFT…onmicrosoft.com` record and `_dmarc`. They are what makes
  `info@juice-gaming.com` work.
- Do not add a wildcard (`*`) record. GitHub warns it allows subdomain takeover.

Changes take up to 24 hours to spread; usually minutes to an hour. Check with
`nslookup juice-gaming.com` (it should list the four `185.199.*` addresses).

**Domain verification (do once).** GitHub org **juice-gaming → Settings → Pages → Add a domain**
→ `juice-gaming.com`. GitHub shows a `TXT` record named `_github-pages-challenge-juice-gaming`
with a value to copy; add it in GoDaddy (Name `_github-pages-challenge-juice-gaming`, type `TXT`),
then click **Verify**. Leave the record in place permanently. It stops anyone else from claiming
the domain on GitHub Pages.

**HTTPS.** Once DNS points at GitHub, Pages issues a certificate (can take up to 24 hours). Then
tick **Enforce HTTPS** in this repo's **Settings → Pages**.

Sources: GitHub Docs,
[Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
and
[Verifying your custom domain for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages).

## 🧭 How this site is built and why

Surveyed on 2026-11-25 across `/home/dortal/development`:

| Front-end | Stack | Hosting |
|---|---|---|
| `juice/merge-battles-admin` | Next.js 15, Tailwind 3, `@juice-gaming/design-tokens` + `ui` | Docker image, inside the boundary only |
| `realestate-app/frontend`, `frontend-v2` (ListHive) | Next.js 16, Tailwind 4 | Runs on the host; not hosted publicly |
| `expenses-app/expenses-web` | Vite 8, React 19, Tailwind 4 | Static `dist/` on S3 behind CloudFront, Terraform in `expenses-infra`, personal AWS account |
| `juice/mocks` | Plain HTML, no build, served with `python3 -m http.server` | Local only |

No repo in the workspace used GitHub Pages or a `CNAME` file before this one; this is the first
public site.

- **Plain HTML, no build**, like `juice/mocks`. The site is three pages of text that change a
  few times a year. A framework would add a build, a lockfile and a weekly dependency PR for no
  benefit, and its runtime JavaScript would contradict the policy's "no scripts" line.
- **Brand from `@juice-gaming/design-tokens`.** `style.css` uses the package's variable names
  and format (`--jg-color-*` RGB channels, `rgb(var(…))`) and the `console` theme's values,
  including the one primary button: blue-600 with a blue-500 ring at 30%. They are copied rather
  than installed because there is no build step, and the package is on GitHub Packages behind a
  token. When the `merge-battles` theme ships, swap the `:root` block.
- **No CI.** The fleet's reusable workflows (`juice-gaming/.github`) are for Node repos with a
  `package.json`; this repo has none, so the weekly dependency sweep skips it by its own rule.
  Pages' built-in `pages-build-deployment` publishes on every push to `main`.
- **GitHub Pages, not S3 + CloudFront.** `expenses-web`'s S3 + CloudFront + Terraform pattern
  is the workspace's other hosting pattern, and it can send security headers (CSP, HSTS,
  framing) that Pages cannot. It also needs an AWS account, keys and Terraform state. Juice
  Gaming has none of those, and a static site with no scripts and no forms gets little from those
  headers. Revisit if the site grows a sign-in page (the planned web account deletion with
  "Sign in with Google"). That page will need JavaScript, a backend call and a CSP.
