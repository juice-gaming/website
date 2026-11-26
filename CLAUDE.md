# website

## Role
The public website for Merge Battles, `juice-gaming/website`, served by GitHub Pages from the
root of `main` at `https://juice-gaming.com`. It exists for Google Play: `/privacy/` is the
privacy policy URL and `/delete-account/` is the Data safety account deletion URL. `README.md` is
the manual: files, editing, DNS, and "How this site is built and why".

## Rules
- **The privacy policy describes this site too.** It says the site has no cookies, analytics,
  trackers or scripts and loads nothing from other sites. Do not add JavaScript, web fonts,
  embeds, images from a CDN or any third-party request.
- **The policy is written for the game as planned** (owner instruction, 2026-11-25): Google
  sign-in, in-app purchases through Google Play Billing, in-app deletion under Settings → Delete
  account, an app-generated device ID, and automatic deletion of guest accounts after 12 months
  without a login. Some of these did not exist when it was written. When one of them changes, the
  policy changes in the same piece of work, and so does the Effective date.
- **The policy must cover every purpose ticked in Play's Data safety form** (`juice/TASKS.md`
  T-12). Analytics on purchase records, player IDs and game events is explained under "Testing
  game settings" (`#testing`), because gameplay configs are A/B-tested on purchases (owner,
  2026-11-25). Add a purpose to the form only together with the policy text for it.
- **Account deletion answers (owner, 2026-11-25, `juice/TASKS.md` T-68):** linked accounts never
  expire; **no deletion on an email alone** — an email gets a reply pointing to Settings → Delete
  account in the game (reinstall + "Continue with Google" if the game is gone), and web deletion
  with Sign in with Google comes later; purchase records up to 7 years, anonymised; a banned
  Google-linked account leaves a one-way hash of its Google ID and the ban reason for 2 years;
  deletion cannot be undone. Both pages state these; change both together.
- **Values the owner has to confirm** are the retention periods: server logs 30 days, security
  records 12 months, purchase records up to 7 years. They appear on both `privacy/` and
  `delete-account/`, so change both.
- The controller is **Dor Tal, trading as Juice Gaming**, contact `info@juice-gaming.com`. No
  postal address is published. Do not invent one.
- `delete-account/` must name the app and developer as on the Play listing: **Merge Battles**,
  **Juice Gaming** (the Play developer name since 2026-11-25).
- Relative links everywhere except `404.html` (root-absolute, because Pages serves it at any depth).
- **Do not delete `CNAME`.** Pages drops the custom domain without it.
- **Every page shares one header and one footer**, copied by hand into each file (there is no
  build to include them). Changing the navigation or the footer means changing `index.html`,
  `privacy/index.html`, `delete-account/index.html` and `404.html` together. The navigation is
  Home, Games, About, Contact (anchors on the home page); Privacy policy and Delete account are in
  the footer. No hamburger: see README, *Design*.
- **Colours come from the logo** and live only in `style.css`'s `:root` blocks, with the WCAG
  contrast of every text/background pair in the comment above them. A new colour pair gets its
  ratio computed and added there; text must reach 4.5:1 in both schemes. Orange `#ff8e12` is
  never text on cream.
- **A layout change must not change the legal wording.** Diff the text inside `<main>` of
  `privacy/` and `delete-account/` before and after.
- Push to `main`. There is no CI, no version and no build.
