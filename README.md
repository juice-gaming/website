# website

The public website for **Merge Battles** by Juice Gaming, at <https://juice-gaming.com>.
Plain static HTML and one CSS file, served by GitHub Pages from the root of `main`.

| URL | File | What |
|---|---|---|
| `/` | `index.html` | Welcome page: hero with the logo and the slogan, **Our games** (`#games`, Merge Battles), **About us** (`#about`), **Contact** (`#contact`) |
| `/privacy/` | `privacy/index.html` | Privacy policy (the Play Console privacy policy URL) |
| `/delete-account/` | `delete-account/index.html` | Account deletion (the Play Console Data safety deletion URL) |
| `/accessibility/` | `accessibility/index.html` | Accessibility statement (הצהרת נגישות), Hebrew and English |
| any missing path | `404.html` | Not found |
| — | `style.css` | All styling, including the palette and its contrast table |
| — | `img/logo-512.png` | The logo, 512 px, transparent. Also the `og:image` (absolute URL, our own domain) and the hero's fallback |
| — | `img/logo-512.webp`, `img/logo-256.webp` | The hero logo for browsers that take WebP (48 KB and 20 KB instead of 232 KB) |
| — | `img/logo-96.png` | The header logo, shown at 40 px (5 KB, 256 colours) |
| — | `img/logo-256.png`, `img/favicon-32.png`, `favicon.ico`, `apple-touch-icon.png` | Logo and icons; `logo-256.png` is no longer referenced by a page and is kept as an asset |
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
  **Merge Battles**, developer **Juice Gaming**. Google Play checks this.

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
- **Palette from the logo, not from `@juice-gaming/design-tokens`.** The site used to copy the
  tokens' `console` theme (the admin's slate and blue). Since 2026-11-26 every colour is sampled
  from the logo (see *Design* below); the `console` theme is the admin's look, not the brand's.
  When the tokens grow a `merge-battles` theme (owner decision D5), it should start from these
  values, and this file should then take its values from it.
- **No CI.** The fleet's reusable workflows (`juice-gaming/.github`) are for Node repos with a
  `package.json`; this repo has none, so the weekly dependency sweep skips it by its own rule.
  Pages' built-in `pages-build-deployment` publishes on every push to `main`.
- **GitHub Pages, not S3 + CloudFront.** `expenses-web`'s S3 + CloudFront + Terraform pattern
  is the workspace's other hosting pattern, and it can send security headers (CSP, HSTS,
  framing) that Pages cannot. It also needs an AWS account, keys and Terraform state. Juice
  Gaming has none of those, and a static site with no scripts and no forms gets little from those
  headers. Revisit if the site grows a sign-in page (the planned web account deletion with
  "Sign in with Google"). That page will need JavaScript, a backend call and a CSP.

## 🎨 Design

Redesigned 2026-11-26 (owner: "a welcome page and a navigation; the whole website should have the
same colour palette as the logo"). The slogan is **"Just a gaming company that cares about
juice."**, kept in the owner's words.

**Layout.** Every page has the same navy header (logo, *Juice Gaming*, and the navigation), and
the same navy footer (slogan, *Legal*: Privacy policy and Delete account, the contact email, and
"no cookies, no trackers and no scripts"). The home page is a hero (slogan as the `h1`, the logo,
two buttons) followed by three sections the navigation links to. `privacy/`, `delete-account/` and
`404.html` put their `h1` in a cream band and their text in a 44rem column; their wording is
unchanged, and checking that is a diff of the text inside `<main>` before and after.

**Navigation without JavaScript.** Four links (Home, Games, About, Contact), always visible, which
wrap under the logo on a phone; they fit on one line at 320 px. There is no hamburger, for two
reasons: hidden navigation is found roughly half as often as visible navigation (NN/g), and the
script-free hamburgers (`<details>`, the checkbox hack) are not accessible without script either
(Cloud Four, Adrian Roselli). If the site grows past about five top-level pages, revisit this.
Privacy and Delete account live in the footer, where people look for legal pages. The header is
not sticky, so it never covers a focused element (WCAG 2.2, 2.4.11 Focus Not Obscured).

**Palette.** Sampled from the full-size logo with Pillow; the text colours are the sampled ones
moved only as far as WCAG 2.2 AA needs. Both schemes are AA, so the site follows
`prefers-color-scheme`: cream in light, navy in dark. The header, the hero and the footer are
navy in both. The table of every text/background pair and its ratio is the comment at the top of
`style.css`; change the colours there and recompute it.

| Token | Value | From the logo |
|---|---|---|
| `--navy` | `#0e1530` | The outline |
| `--orange` | `#ff8e12` | The juice; buttons and accents, never text on cream (2.2:1) |
| `--orange-deep` | `#b4400c` | Juice, darkened: links and the button edge on cream (5.5:1) |
| `--red` | `#e3222a` | The straw stripes (hero glow only) |
| `--yellow` | `#fedc8a` | The highlights; "juice" in the slogan, the focus ring on navy |
| `--cream` | `#fdf6e9` | The straw's white; text on navy |

**Type.** The system font stack (`system-ui`, then each platform's UI font): no web font, which the
privacy policy forbids anyway, and no download. Headings are heavy (800) and tight; text is 17 px
with a line height of 1.6 and a measure of about 70 characters.

**Accessibility.** A skip link, a 3 px focus ring everywhere (navy on cream, yellow on navy, 13:1
or more), 44 px touch targets in the navigation, one `h1` per page, landmarks, and `alt` text on
the hero logo only (the header logo sits next to the words *Juice Gaming*, so it is `alt=""`). The
only motion is the hero glass floating 8 px, and only under `prefers-reduced-motion:
no-preference`.

**Weight.** The home page is about 60 KB: 2 KB of HTML and 4 KB of CSS gzipped, the 5 KB header
logo, and a 48 KB WebP hero logo (the 232 KB PNG only where WebP is unsupported). No request
leaves `juice-gaming.com`.

**Checks.** `npx html-validate index.html 404.html privacy/index.html delete-account/index.html`
passes with its recommended rules. No page scrolls sideways at 320, 390, 768 or 1440 px.

Sources: NN/g, [Hamburger Menus and Hidden Navigation Hurt UX Metrics](https://www.nngroup.com/articles/hamburger-menus/);
Cloud Four, [A details element as a burger menu is not accessible](https://cloudfour.com/thinks/a-details-element-as-a-burger-menu-is-not-accessible/);
Adrian Roselli, [CSS-only Widgets Are Inaccessible](https://adrianroselli.com/2023/03/css-only-widgets-are-inaccessible.html);
W3C, [What's New in WCAG 2.2](https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/) and
[Understanding 1.4.11 Non-text Contrast](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast);
[Modern Font Stacks](https://modernfontstacks.com/);
web.dev, [prefers-reduced-motion](https://web.dev/articles/prefers-reduced-motion);
Game Developer, [Best Practices: Game Websites and Landing Pages](https://www.gamedeveloper.com/game-platforms/best-practices-game-websites-and-landing-pages).
