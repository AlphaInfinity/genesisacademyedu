# Genesis Academy – static mirror

A static copy of https://www.genesisacademyedu.com, taken from the live Weebly site on
2026-09-29. Pages, images, theme files, fonts and the Weebly CDN assets they need are all
in this repository with relative URLs. The site renders without contacting Weebly.

| Path | Contents |
| --- | --- |
| `index.html`, `about.html`, `college-planning.html`, `sat.html`, `act.html`, `isee.html`, `ap.html`, `academic-tutoring.html` | The 8 pages, with their original filenames |
| `files/` | Site theme CSS/JS (`main_style.css`, `theme/`) |
| `uploads/` | Site images (logo, content, section backgrounds) |
| `assets/cdn2.editmysite.com/` | Weebly platform CSS/JS, icons and fonts that were served from Weebly's CDN |
| `cdn-cgi/` | Cloudflare email-decode script (turns the obfuscated `email-protection` links back into `mailto:` links) |
| `.nojekyll` | Tells GitHub Pages to serve the files as-is |

The files under `assets/` and `files/theme/` are Weebly's platform and theme code. They are
included only so the copy renders identically. Replacing them is part of moving fully off
Weebly.

## Preview locally

```sh
python3 -m http.server 8000
# open http://localhost:8000/
```

Use a local web server. Opening the files directly (`file://`) breaks some scripts.

## Publish on GitHub Pages (future steps)

1. Push this repository to GitHub.
2. In **Settings → Pages**, choose **Deploy from a branch**, then select the default branch and
   `/ (root)`.
3. Add a `CNAME` file at the repository root containing `www.genesisacademyedu.com`, or set the
   custom domain under **Settings → Pages**, which commits that file for you. The file is
   deliberately not in the repository yet.
4. At the DNS provider:
   - `www` → `CNAME` record pointing to `<github-user>.github.io`
   - apex `genesisacademyedu.com` (optional redirect to www) → `A` records `185.199.108.153`,
     `185.199.109.153`, `185.199.110.153`, `185.199.111.153` (and `AAAA` records
     `2606:50c0:8000::153` … `8003::153` if IPv6 is wanted)
5. After DNS resolves, turn on **Enforce HTTPS** in **Settings → Pages**.
6. Cancel the Weebly plan only after the GitHub Pages site is live and the newsletter form is
   confirmed working (below).

## Activate the newsletter form

The footer "Subscribe Today!" form on every page is live: it posts to Formspree at
`https://formspree.io/f/xkjgyqkg`, and sign-ups go to that Formspree form's owner.

To switch to a different Formspree form later, replace the ID in every page's form `action`:
`sed -i 's/xkjgyqkg/NEW_ID/' *.html`.

## Weebly-dependent features (will not work on GitHub Pages)

Unless marked replaced, these are left exactly as they were on Weebly. Each one needs a replacement or removal.

| Feature | Page(s) | What depends on Weebly |
| --- | --- | --- |
| ~~**"Subscribe Today!" email form**~~ — **replaced** (footer, form id `form-165633829848770969`) | all 8 pages | Now posts to Formspree (`https://formspree.io/f/xkjgyqkg`; see [Activate the newsletter form](#activate-the-newsletter-form)) with a single `email` field. The Weebly hidden fields and Weebly reCAPTCHA were removed. |
| **Weebly Customer Accounts** (member login bootstrap) | all 8 pages | Weebly's JS calls `/ajax/api/JsonRPC/CustomerAccounts/` on page load. On a static host this request fails without any visible effect. |
| **Weebly Commerce** (mini cart) | all 8 pages | Calls `/ajax/api/JsonRPC/Commerce/` (`Checkout::getMiniCart`) on page load. Fails with no visible effect; the site has no Weebly store. |
| **Weebly analytics (Snowplow "snowday")** | all 8 pages | `assets/.../js/wsnbn/snowday262.js` (bundled locally) sends page views to `ec.editmysite.com` for Weebly's site stats. It keeps sending to Weebly, and those stats disappear with the Weebly account. |
| **Weebly's Google Analytics tracker** (`ga.js`, account `UA-7870337-1`) | all 8 pages | Weebly's legacy platform-wide tracker, loaded from `google-analytics.com`. Universal Analytics has been retired. Remove it, or replace it with the owner's own analytics. |
| **Lazy-loaded Weebly script chunks** | all 8 pages | Weebly's `main.js` loads extra code on demand from `https://` + `window.ASSETS_BASE` (`cdn2.editmysite.com`). None of the pages or menu interactions tested here trigger this, but any feature that does would still reach Weebly's CDN. |

### Other external services (keep working on GitHub Pages)

| Feature | Page(s) | Notes |
| --- | --- | --- |
| Stripe Buy Buttons (9) | `ap.html` | Loaded from `js.stripe.com`, and must be loaded from there. Independent of Weebly. |
| Cloudflare email obfuscation | all 8 pages (footer email icon, contact emails) | Decoded in the browser by the bundled `cdn-cgi/.../email-decode.min.js`. No Cloudflare account is needed. |
| Social links, UC / Common App links | footer, `college-planning.html` | Ordinary outbound links. |
