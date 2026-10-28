# PVN Kalkulators

Static multi-page site for a Latvian/EU VAT (PVN) calculator, live at pvnkalkulators.eu.

## Stack

- Plain HTML/CSS/JS. No build step, no framework, no package.json — `npm install` is not needed and will fail (no package.json exists).
- Hosted on GitHub Pages (repo `ungursedgars/pvn-kalkulators`, branch `main`, source `/` root).
- Custom domain `pvnkalkulators.eu` via the `CNAME` file plus DNS records at Zone.eu (dashboard: eu.myzone.app) — 4 A records on the apex pointing at GitHub Pages IPs, `www` CNAME to the apex.
- Search Console: both a Domain property and a URL-prefix property (`https://pvnkalkulators.eu/`, added 2026-10-28) exist. The plain `sitemap.xml` entry was stuck on "Couldn't fetch" for 3 months although the live test passed; the same file submitted as `sitemap.xml?v=2` was read immediately (Success, 12 pages, 2026-10-28). Keep the file at `/sitemap.xml` (also referenced in `robots.txt`) - don't rename or move it, Google reads it through the `?v=2` entry.
- Contact form (`kontakti.html`) submits via Web3Forms (`fetch`, JSON body) — the access key in the page is a public key by design; no email address appears anywhere in the source.

## Pages

- `index.html` — the calculator itself: country picker with flags, rate buttons, live bidirectional net/gross calculation, floating mini-calculator (insert result into net/gross field), decimal-separator toggle (`#sepBtn`, comma vs period — independent of UI language), full LV/RU/EN i18n.
- `buj.html`, `likmes.html` — FAQ and VAT-rates content, each with its own FAQPage JSON-LD. Moved out of index.html so the calculator page stays focused.
- `instrukcijas.html` — step-by-step usage guide, covering the calculator and the floating mini-calculator's "insert into field" buttons.
- `pvn-latvija.html`, `pvn-rekinos.html`, `pvn-e-komercija.html` — long-form guide pages (Article JSON-LD), added for AdSense content depth.
- `par-vietni.html`, `lietosanas-noteikumi.html` — About and Terms of use (trust pages).
- `kontakti.html`, `privatuma-politika.html` — contact form and privacy policy.
- `kalkulators-vietnei.html` — instructions page for the embeddable widget (LV/RU/EN, WebPage JSON-LD): live preview iframe, language/theme selects and a copy-snippet button. The configurator (`#embedCfg`) sits outside `#guideBody` because `apply()` rewrites the article; it has the head AdSense script and the "Raksta beigas" unit like other content pages.
- `embed.html` — the widget itself (the iframe target other sites embed) and the one deliberate exception to the shared template: no header/footer/menu, no AdSense, GA, consent dialog, cookies or localStorage, `noindex`, not in `sitemap.xml`. Params `?lang=lv|ru|en&theme=light|dark|auto`. The LV rates are hard-coded in its `data-r` buttons, so update them together with the rest of the site when rates change (embedders get the change automatically). Its only link is the visible brand link `pvnkalkulators.eu` (with `utm_source=embed`); keep it brand-only and visible, never keyword anchors or hidden links (Google's link-spam policy calls out keyword-rich links in widgets), and don't add a link to the copy snippet outside the iframe. The snippet uses `height="360"`; if the widget layout changes, re-measure at 220-460 px widths with a large amount so the brand link stays inside 360 px.
- The AdSense script (`adsbygoogle.js`) sits in `<head>` of every content page, unconditionally, right after the Consent Mode default script — Google's reviewers and crawlers must see ad code, which is what both approved competitors (pvnkalkulators.lv/.com) do. It is deliberately absent on `kontakti`, `privatuma-politika` and `lietosanas-noteikumi` (form/legal pages have no publisher content — AdSense policy). Two explicit `<ins class="adsbygoogle">` units are placed: "Zem kalkulatora" (slot `9869608934`) above `#homeInfo` on the homepage, and "Raksta beigas" (slot `8444846629`) before the back-link on the other content pages. Google's snippet repeats the loader `<script>` - do not paste that part again, the loader is already in `<head>`.
- All 12 site pages (everything except `embed.html`) share the same header: logo, LV/RU/EN lang-switch, and a `☰` dropdown menu (`#menuDropdown`/`#menuBtn`/`#menuList`) linking to Rokasgrāmata/PVN rēķinos/PVN e-komercijā/Instrukcijas/BUJ/Likmes/Par vietni/Kontakti; footers also link Kalkulators tavai vietnei, Lietošanas noteikumi and Privātuma politika. No page should be a navigation dead end.
- `likmes.html` keeps the 27-country data in the `EU`/`RATE_DESC`/`ZERO_DESC` JS arrays; the static LV table + per-country `<details>` in `#ratesTable` are generated from those arrays (`buildRatesTable`) so crawlers see the data — if you change the arrays, regenerate the static block the same way rather than editing it by hand.
- Internal links are extensionless (`href="buj"`, `href="/"` for the homepage, etc.) — GitHub Pages serves `foo.html` for a request to `/foo`, so this keeps `.html` out of the address bar. Follow this convention for any new internal links or pages.

## Content style

- The site operator is **SIA UNG STORE** (Latvia). Keep that name consistent in every footer, in About, Terms, the privacy policy's data-controller section and the JSON-LD `publisher`.
- Source of truth for Latvian rates is VID's "Pievienotās vērtības nodokļa likmes" page (vid.gov.lv); the Ministry of Finance rates page is a stale 2021 version - don't link or cite it. Current facts (verified 2026-09-05): 21% standard; 12% medicines/medical devices, infant food, domestic regular passenger transport, tourist accommodation, firewood and heat for households, fresh fruit/berries/vegetables, bread, milk, poultry, eggs; 5% books and press incl. electronic; 0% exports, intra-EU B2B supplies, international passenger transport. VAT return within 20 days, payment within 23 days after the period. Registration threshold 50 000 EUR; special small-business scheme (Art. 139.2) since 1 July 2025.
- Guide pages show a fixed `LAST_CHECKED` date set in each page's script. Change it only when the rates and text were actually re-verified against VID/EC. Never generate today's date automatically - that misrepresents the content (Google Publisher Policies).

- **Never use em-dashes (—) in any site content** — page copy, titles, meta descriptions, JSON-LD, all languages. Use a plain hyphen (-) instead. The owner's explicit preference: em-dashes make the page look AI-generated. (This rule is about site content; CLAUDE.md itself is exempt.)

## Workflow

- **Edit freely, but only `git push` when the user explicitly asks** (e.g. "pusho", "publish this"). This is an early-stage project — changes come in quick succession and shouldn't go live on every single edit.
- **Exception: in cloud/remote sessions** (Claude Code on the web, not a local checkout), commit, push, and merge straight to `main` without asking first — the user has asked for this so the live site stays current automatically.
- Don't verify UI changes yourself in a browser preview after every edit — just make the change and describe it. The user tests it themselves. Only open a preview if asked to, or when reproducing a bug the user reported.
- When adding a new page, replicate the existing per-page i18n pattern (an `I18N` object with `lv`/`ru`/`en` keys, an `apply(lang)` function that updates the DOM, `initialLang()` that checks `localStorage` then `navigator.language`) and the shared dropdown-menu markup/CSS/JS — don't introduce a different pattern.
- The decimal-separator setting and the UI language setting are deliberately independent — don't couple them.
- The cookie-consent dialogs (`#cookieBanner`, `#cookiePrefs`) are inserted by a small inline script with empty text nodes; `apply(lang)` fills every string from I18N. Keep it that way — the same ~100 words of dialog text must not sit in every page's static HTML (Google's duplicate-content guidance for AdSense).
- Two consent layers, by design: **ad consent belongs to Google's certified GDPR message** (AdSense → Privacy & messaging, published since 2026-07-14; delivered by `adsbygoogle.js`, it updates Consent Mode `ad_*` itself). Our own dialog covers **analytics only** (`analytics_storage`), stores `{analytics}` in `localStorage['pvn-consent-v2']`, and is shown only after Google's message is answered (`googlefc.callbackQueue` `CONSENT_DATA_READY`, 4 s fallback, immediate on pages without the ad script). `/#reklamas` re-opens Google's message via `googlefc.showRevocationMessage()`, handled on click, on `hashchange` and on load; on pages without the ad script the link is left to navigate to the homepage. When the message is not available (site not approved yet, ad blocker) the dialog opens instead and shows the `adsMsgUnavailable` string, so the link is never dead. Do not add an ads toggle back to our dialog.
- Every guide page must carry a real disclaimer/sources block, but vary the wording per page — identical sentences across pages count against the site.
- Google Consent Mode v2: an inline `<head>` script defines `gtag` and pushes `consent default` (all denied) *before* the AdSense `<script>` tag; the consent dialog pushes `consent update`. Never move the AdSense tag above the default. Before consent Google serves only non-personalised, cookieless ads; the dialog's "Reklāmas" toggle therefore governs personalisation, not whether the script loads — the dialog text and privacy policy say so. Personalised ads in the EEA additionally require Google's certified CMP (AdSense → Privacy & messaging), enabled by the owner in the dashboard.

## Known quirks

- `index.html`'s footer nav element ids use a `footer` prefix (`footerHome`, `footerInstrukcijas`, ...); all other pages use a `foot` prefix (`footHome`, `footInstrukcijas`, ...). Pre-existing inconsistency, harmless — just match whichever file you're editing.
