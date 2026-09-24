# Giftgenie — pre-launch site

The public marketing site for **gift-genie.co.uk**: a landing page and a privacy
policy, built to be credible for affiliate network and advertiser manual review.

It is deliberately a **plain static site**: no build step, no framework, no
dependencies. There is nothing to compile, so the site can be reviewed, diffed
and deployed by anyone, and there is no toolchain to keep patched.

This repository contains **only the site**. The Giftgenie product backend is a
separate project, so a deploy of one cannot break the other.

## Files

```
index.html    Landing page
privacy.html  Privacy Policy
404.html      Not-found page (used automatically by most static hosts)
styles.css    The only stylesheet, shared by all pages
README.md     This file
```

## Local preview

Any static server works. With Python already installed:

```bash
python -m http.server 8899 --bind 127.0.0.1
# open http://127.0.0.1:8899/
```

Or just open `index.html` directly in a browser — there is nothing to compile.

## Deploying to Vercel

The site files sit at the **repository root**, so Vercel needs no configuration:
there is no Root Directory to set and no build command to add.

1. In Vercel: **Add New → Project → Import Git Repository**, then pick this repo.
2. Framework Preset: **Other**. Leave Build Command, Output Directory and
   Install Command **empty**.
3. **Deploy.** Vercel serves the repo root as static files.
4. Add the domains `gift-genie.co.uk` and `www.gift-genie.co.uk` under
   **Settings → Domains**, then pick which one redirects to the other.

Vercel uses `404.html` automatically for unmatched paths. Every push to `main`
creates a production deployment; other branches get preview URLs.

> **Do not set a Root Directory.** Because the files are already at the root,
> setting one (for example to `web`) will break the build. This is the single
> most likely misconfiguration for this project.

### Other hosts

| Host | Setup |
| --- | --- |
| **Netlify** | New site → drag this folder in, or connect the repo with publish directory `.` and no build command. |
| **Cloudflare Pages** | Connect the repo, build command empty, output directory `.`. |
| **GitHub Pages** | Settings → Pages → deploy from branch, folder `/` (root). |
| **Any VPS / nginx** | Copy the repo contents to the web root, e.g. `/var/www/gift-genie`. |

### Pointing the domain

1. In the host's dashboard, add the custom domain `gift-genie.co.uk` **and**
   `www.gift-genie.co.uk`.
2. Create the DNS records the host gives you. Typically:
   - `A` record for the apex (`@`) → the host's IP, **or**
   - `CNAME` for `www` → the host's hostname
   - an `ALIAS`/`ANAME` or the host's "apex" record if you want the apex on a CDN.
3. Redirect one to the other so you do not serve duplicate content. Serving
   `www` and redirecting the apex (or vice versa) is the usual choice.
4. Enable HTTPS. Every host above issues a free certificate automatically; wait
   for it to go active before submitting the site for review.

DNS for this domain is managed at **One.com**. Nameservers and mail records live
there; the web host only supplies the records to add. Change the mail records
with care — the domain's MX record is what makes `hello@gift-genie.co.uk` work.

`.co.uk` domains are administered by Nominet. Make sure the registrant contact
details are correct and the domain has not lapsed — reviewers do check that the
site resolves on the domain you gave them.

### Caching

Asset filenames are not hashed, so let host defaults apply or set a short cache
lifetime for HTML and a longer one for `styles.css`. If you edit `styles.css` and
visitors see a stale version, bump the query string in the `<link>` tag:

```html
<link rel="stylesheet" href="styles.css?v=2" />
```

## Before you submit for review

### 1. Operator details (already filled in)

The site publishes real operator details and contains **no** bracketed
placeholders. Verify with:

```bash
grep -n "\[" *.html
```

This should return nothing.

Current published details:

| Field | Value |
| --- | --- |
| Legal entity | CABJOY LIMITED |
| Company number | 16604484 |
| Registered office | 6 Mayflower Close, Hartwell, Aylesbury, England, HP17 8QH |
| Email | hello@gift-genie.co.uk |

VAT number and phone number are intentionally **not** published: no VAT number
has been supplied for CABJOY LIMITED, and the site deliberately exposes email as
its only contact route. Do not reintroduce a VAT number or phone number unless
you have confirmed values for **this** entity.

Placement rules, so future edits do not drift:

- On the **privacy page** (section 1 and the footer) publish: legal entity,
  company number, registered office, and email.
- The Companies (Trading Disclosures) Regulations 2008 require a UK company's
  registered name, registered office and company number on its website. The
  **landing page** therefore carries a single minimal legal line with the entity
  name, company number and registered office, e.g.
  `Giftgenie is operated by CABJOY LIMITED, company number 16604484.`
- Keep that landing-page line short and low-key. It must not grow into a full
  company block, and must carry **no** VAT number and **no** phone number.
- Directors' names are **not** required by the trading-disclosure rules. They are
  deliberately omitted. Add them only if there is a specific reason to.
- The `.company` CSS class is used on the privacy page. If reused on the landing
  page, keep it to the single legal line.

### Email

The site publishes a branded mailbox on the domain:

| Address | Purpose |
| --- | --- |
| `hello@gift-genie.co.uk` | The address published everywhere on the site |
| `partners@` | Affiliate network and advertising applications (alias) |
| `privacy@` | Data-subject requests (alias) |

Mail is hosted via the domain's MX record (`mailserver.livemail.co.uk`), with an
SPF record of `v=spf1 mx a include:_spf.livemail.co.uk ~all`.

Notes for future edits:

- All three addresses are aliases into one inbox, so switching any given page
  between `hello@` and `privacy@` is cosmetic, not functional. The privacy page
  currently uses `hello@` throughout for consistency.
- **Add a DMARC record** at `_dmarc.gift-genie.co.uk`, e.g.
  `v=DMARC1; p=none; rua=mailto:hello@gift-genie.co.uk`, and tighten it to
  `p=quarantine` once you have reviewed the reports. Affiliate networks
  increasingly require DMARC, and without it deliverability can suffer.
- Do not fall back to a personal or Gmail address. A branded address is part of
  what makes the site credible at review.
- If mail ever stops working, remove the address from the site rather than
  leaving a bounce in place: a bouncing contact route is a common reason for
  affiliate review rejection.

### 2. Do not add unverifiable claims

The site deliberately avoids them, and it should stay that way until each claim
is genuinely true:

- no traffic numbers, user counts or conversion rates;
- no named partnerships or "as approved by" statements;
- no implied endorsement by any retailer or brand;
- no testimonials or logos you do not have written permission to use;
- no claim that retailer integrations are live.

The roadmap section is labelled **In development**, and the landing page presents
the current stage as a private beta with curated product coverage that is
expanding. No partnerships, live integrations, live pricing or guaranteed
accuracy are claimed anywhere.

### Copy approach: confident, not apologetic

Public copy should read as a real product. Keep it specific and confident rather
than listing what is not ready yet — reviewers read "there are no live retailer
integrations yet" as a site that is not commercially operational.

The underlying facts must stay true, but they do not need to be volunteered.
Avoid these phrasings on the public pages:

- "no live retailer integrations"
- "no affiliate links are active"
- "not currently partnered with"
- "building in the open"
- "today we focus on"
- "experimental", "placeholder" or pre-alpha framing

Those points remain documented here, where reviewers of the code can see them,
rather than on the marketing pages.

The **affiliate disclosure stays on both pages**. It is a standard, forward-looking
statement ("may earn commission"), so it does not contradict a site that has no
active affiliate links yet — do not add a line asserting that links are inactive.

### 3. Keep the privacy policy true

The policy states that the site sets **no cookies** and runs **no third-party
analytics**. That is true today because the pages load nothing external — no
fonts, no CDNs, no scripts, no images from other domains.

**If you add analytics, a chat widget, embedded videos, a font CDN or a
newsletter form, you must update the policy and add a cookie banner first.** The
quickest way to fail a review is for the policy to contradict what the page
actually loads.

To verify nothing external was introduced:

```bash
grep -nE '(src|href)="https?://' *.html
```

The only matches should be `<link rel="canonical">` tags. (A hyperlink such as
the ICO address does not load anything.)

## Design notes

- **Mobile-first and responsive.** Verified with no horizontal overflow at 320px
  and 390px widths.
- **Accessible.** Semantic landmarks, a skip link, visible focus styles,
  sufficient contrast and `prefers-reduced-motion` support.
- **No JavaScript dependency.** Navigation and content are plain HTML and CSS.
  The only script is a three-line year stamp that degrades to a correct static
  value.
- **Brand colours** live in CSS custom properties at the top of `styles.css`.
  Change `--brand` there to re-skin the whole site.
- **Links are relative filenames** (`privacy.html`, `index.html`), so the site
  works unchanged on any host and from the filesystem.

## Progress note

This is a pre-launch site. It describes what Giftgenie is building and is open
about the current stage. Update the copy as the product becomes real, and keep the
"last updated" date on the privacy policy accurate.
