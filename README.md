# Tess Lin — Personal Portfolio Website

A single-file, bilingual (Traditional Chinese / English) personal portfolio and résumé site — no build step, no framework, just HTML/CSS/JS that runs directly in the browser.

## Features

- **Bilingual content with one toggle** — every piece of static copy is driven by a `data-i18n` attribute matched against a `zh`/`en` dictionary, so the whole page re-renders in place without a reload. The chosen language persists via `localStorage`.
- **Expandable project & publication detail cards** — clicking a project or publication opens a modal with a longer write-up, tags, and links, sourced from a single `DETAILS` object (also bilingual).
- **Downloadable vCard (.vcf)** — generates a contact card client-side from a template string and triggers a browser download, no server involved.
- **Scannable QR code** — encodes the same vCard data via [qrcodejs](https://github.com/davidshimjs/qrcodejs) (loaded from a CDN) so a phone camera can save the contact directly.
- **Decorative animated background** — a lightweight generated SVG node-graph (drawn and animated in plain JS) behind the hero and contact sections.
- **Fully responsive** — single-column layout under ~880px, with further breakpoint adjustments down to small phones.
- **Resilient to third-party failures** — the vCard download handler is wired up independently of the QR code library, and the QR code generation is wrapped in a `try/catch` with a visible fallback message, so a blocked CDN request can't take down the rest of the page.

## Tech stack

- Vanilla HTML, CSS (custom properties for theming), and JavaScript — no build tooling, no dependencies to install
- Google Fonts: `Fraunces`, `Anton`, `IBM Plex Mono`, `Noto Serif TC`, `Noto Sans TC`
- [qrcodejs](https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js) via CDN, for the contact QR code

## Project structure

```
.
└── index.html   — everything: markup, styles, and scripts in one file
```

Everything lives in one file by design, so the whole site can be dropped anywhere a static file can be served from.

## Getting started

### Run locally

No build step required — just open `index.html` in a browser, or serve it locally if you'd rather avoid `file://` restrictions on some browsers:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

### Deploy

Because it's a single static HTML file, it deploys anywhere that serves static files:

- **GitHub Pages**: push this repo, enable Pages in the repo settings, point it at the branch/root containing `index.html`.
- Any static host (Netlify, Vercel, Cloudflare Pages, S3, etc.) works the same way — just upload the file.

## Content & customization

All editable content is centralized in two places inside the `<script>` block at the bottom of `index.html`:

### 1. General page text — the `I18N` object

Every string with a `data-i18n="some-key"` attribute in the HTML pulls its content from `I18N.zh["some-key"]` and `I18N.en["some-key"]`. To change any label, heading, or paragraph:

1. Search the HTML for the relevant `data-i18n="..."` attribute to find its key.
2. Update the matching key under **both** `I18N.zh` and `I18N.en` — the two must be kept in sync manually; there's no automatic translation.

### 2. Project & publication detail modals — the `DETAILS` object

Each project card (`data-detail="proj-xxx"`) and publication row (`data-detail="pub-xxx"`) links to an entry in `DETAILS`, which contains a `zh` and `en` version of:

- `tag`, `title`, `sub`, `period` — header metadata
- `body` — an array of `{ h: "..." }` (subheading) and `{ p: "..." }` (paragraph) blocks, rendered in order
- `images` — an array of `{ caption: "..." }` placeholders
- `tags` — chip labels shown at the bottom of the modal
- `links` — external reference links (paper PDFs, certificates, project pages, etc.)

**Adding a real image to a modal:** placeholder entries render as a dashed-border box with the caption. Once you have an actual image, add a `src` field:

```js
// before
{ caption: "System architecture diagram" }

// after
{ caption: "System architecture diagram", src: "images/system-architecture.jpg" }
```

The script automatically switches from the placeholder box to an `<img>` tag once `src` is present — no other code changes needed. Create an `images/` folder alongside `index.html` for this.

**Adding a new project or publication:**

1. Add a new entry to `DETAILS` with a unique key (e.g. `"proj-new-thing"`), following the same `zh`/`en` shape as existing entries.
2. Add a corresponding card in the HTML (`<div class="proj-card" data-detail="proj-new-thing">...</div>` for a project, or a `<div class="pub-item clickable" data-detail="pub-new-thing">...</div>` for a publication) with a `data-i18n` key for its summary text, and register that key in `I18N`.

### 3. Contact info / vCard

The vCard template is a plain array of strings near the bottom of the script (search for `BEGIN:VCARD`). Update the name, phone, email, org, and note fields there — the QR code and the "Download vCard" button both read from this same template automatically.

## Browser support & accessibility notes

- Respects `prefers-reduced-motion`: both the smooth-scroll behavior and the animated background dots are disabled for users who've requested reduced motion at the OS level.
- The language toggle updates `document.documentElement.lang` and relevant `aria-label`s so assistive technology reflects the current language.
- Background decoration and the QR code CDN script are both non-critical paths — if either fails (blocked network, ad blocker, offline), the rest of the page — including navigation, content, and the vCard download — keeps working.

## Credits

- Fonts via [Google Fonts](https://fonts.google.com/)
- QR code generation via [qrcodejs](https://github.com/davidshimjs/qrcodejs)

## License

Add a license of your choice here. Note that the page content itself (résumé text, project descriptions, personal contact details) is personal information — a code license would typically cover the markup/styles/scripts, not the biographical content.
