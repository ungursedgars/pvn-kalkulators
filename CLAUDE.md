# PVN Kalkulators

Static multi-page site for a Latvian/EU VAT (PVN) calculator, live at pvnkalkulators.eu.

## Stack

- Plain HTML/CSS/JS. No build step, no framework, no package.json — `npm install` is not needed and will fail (no package.json exists).
- Hosted on GitHub Pages (repo `ungursedgars/pvn-kalkulators`, branch `main`, source `/` root).
- Custom domain `pvnkalkulators.eu` via the `CNAME` file plus DNS records at Zone.eu (dashboard: eu.myzone.app) — 4 A records on the apex pointing at GitHub Pages IPs, `www` CNAME to the apex.
- Contact form (`kontakti.html`) submits via Web3Forms (`fetch`, JSON body) — the access key in the page is a public key by design; no email address appears anywhere in the source.

## Pages

- `index.html` — the calculator itself: country picker with flags, rate buttons, live bidirectional net/gross calculation, floating mini-calculator (insert result into net/gross field), decimal-separator toggle (`#sepBtn`, comma vs period — independent of UI language), full LV/RU/EN i18n.
- `buj.html`, `likmes.html` — FAQ and VAT-rates content, each with its own FAQPage JSON-LD. Moved out of index.html so the calculator page stays focused.
- `instrukcijas.html` — step-by-step usage guide, covering the calculator and the floating mini-calculator's "insert into field" buttons.
- `kontakti.html`, `privatuma-politika.html` — contact form and privacy policy.
- All 6 pages share the same header: logo, LV/RU/EN lang-switch, and a `☰` dropdown menu (`#menuDropdown`/`#menuBtn`/`#menuList`) linking to Instrukcijas/BUJ/Likmes/Kontakti. No page should be a navigation dead end.
- Internal links are extensionless (`href="buj"`, `href="/"` for the homepage, etc.) — GitHub Pages serves `foo.html` for a request to `/foo`, so this keeps `.html` out of the address bar. Follow this convention for any new internal links or pages.

## Content style

- **Never use em-dashes (—) in any site content** — page copy, titles, meta descriptions, JSON-LD, all languages. Use a plain hyphen (-) instead. The owner's explicit preference: em-dashes make the page look AI-generated. (This rule is about site content; CLAUDE.md itself is exempt.)

## Workflow

- **Edit freely, but only `git push` when the user explicitly asks** (e.g. "pusho", "publish this"). This is an early-stage project — changes come in quick succession and shouldn't go live on every single edit.
- **Exception: in cloud/remote sessions** (Claude Code on the web, not a local checkout), commit, push, and merge straight to `main` without asking first — the user has asked for this so the live site stays current automatically.
- Don't verify UI changes yourself in a browser preview after every edit — just make the change and describe it. The user tests it themselves. Only open a preview if asked to, or when reproducing a bug the user reported.
- When adding a new page, replicate the existing per-page i18n pattern (an `I18N` object with `lv`/`ru`/`en` keys, an `apply(lang)` function that updates the DOM, `initialLang()` that checks `localStorage` then `navigator.language`) and the shared dropdown-menu markup/CSS/JS — don't introduce a different pattern.
- The decimal-separator setting and the UI language setting are deliberately independent — don't couple them.

## Known quirks

- `index.html`'s footer nav element ids use a `footer` prefix (`footerHome`, `footerInstrukcijas`, ...); the other 5 pages use a `foot` prefix (`footHome`, `footInstrukcijas`, ...). Pre-existing inconsistency, harmless — just match whichever file you're editing.
