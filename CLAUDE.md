# Filmmakers of the Desert — Website

Custom website for **Filmmakers of the Desert (FOD)**, a filmmaking community in the desert region.

## Mission

To create a growing central network for people in the desert who want to actively make films of their own and/or collaborate with others. The network is a platform for local filmmakers to grow their skills through an ongoing string of group projects, as well as through their own films.

## The Core Constraint

**Non-technical FOD members must be able to edit all changing content themselves, through a browser, without touching code and without the developer's involvement.**

This is the requirement that drives every technical decision below. When making choices, favor whatever keeps the org self-sufficient — even at the cost of some flexibility.

## Stack

| Layer | Choice | Why |
|---|---|---|
| Site generator | Eleventy (11ty) | Minimal, no framework, plain HTML-ish templates |
| Templates | Nunjucks (`.njk`) | Closest thing to writing plain HTML |
| CMS | Decap CMS at `/admin` | Free, open source, content stays in this repo |
| CMS auth | DecapBridge | Editors log in by email/Google — no GitHub account needed |
| Hosting | Cloudflare Pages | Free, auto-deploys on every push to `main` |
| Styling | Hand-written CSS | No Tailwind, no framework |

Total running cost is the domain only (~$12/year).

### Why a build step at all

The original plan was plain HTML files with no build. That does not work with a CMS: Decap writes content to the repo as markdown and YAML, and plain HTML can't read those files. Eleventy is the lightest way to turn that content into static pages. It stays a static site — Cloudflare Pages runs the build on push and serves plain HTML.

## Repo Structure

```
filmmakers-of-the-desert/
├── .eleventy.js              # Eleventy config
├── package.json
├── src/
│   ├── _includes/            # base layout + nav/footer partials
│   ├── _data/
│   │   └── site.json         # global settings (edited via CMS)
│   ├── content/
│   │   ├── meetings/         # one file per meeting
│   │   ├── projects/         # one file per project (YouTube links)
│   │   ├── members/          # one file per member
│   │   ├── founders/         # one file per founder
│   │   └── photos/           # one file per gallery photo
│   ├── admin/
│   │   ├── index.html        # loads Decap CMS
│   │   └── config.yml        # CMS schema — see separate file
│   ├── css/style.css
│   ├── js/main.js
│   ├── images/
│   │   └── uploads/          # CMS image uploads land here
│   ├── index.njk             # Home
│   ├── community.njk         # Community
│   └── contact.njk           # Contact
└── CLAUDE.md
```

## Content Model

Everything the org edits is **structured fields, not free-form page content.** This is deliberate: an editor filling in a "Location" box cannot break the layout. Do not add a rich-text "page body" field that lets them edit whole pages — that reintroduces the ability to break things.

- **Site settings** (single file) — contact email, role-form URL, mission statement, meeting cadence line
- **Meetings** — date, start time, location name, address, optional note, cancelled flag
- **Projects** — title, YouTube URL, description, date, optional still image, roles-needed flag
- **Members** — name, role (dropdown from fixed list), contact, optional photo, short bio
- **Founders** — name, photo, bio, contact
- **Photos** — image, caption, gallery (Meetings or Shoots), date

The role dropdown is a fixed list in `admin/config.yml`. Members pick from it; they can't invent new roles. Adding a role is a one-line config change.

## Pages

### Home (`index.njk`)
Logo/name, mission statement, the "last Wednesday of each month" cadence line, next upcoming meeting pulled from the meetings collection, founder cards, nav.

### Community (`community.njk`)
Meeting photos gallery, shoot photos gallery with the disclosure statement, projects list with embedded YouTube videos, members grouped by role with contact info.

### Contact (`contact.njk`)
FOD email, link to the role signup form.

## Visual Direction

The reference the founders gave is Andrei Jay's creative coding site. What they responded to, specifically: **it reads as made by an artist, not an agency**, and **it uses lowercase informality.** Those two qualities drive the visual system.

### Governing principle: handcraft the frame, standardize the contents

Everything built once — wordmark, nav, section headings, footer, page structure — is where the hand shows. Everything volunteers add later — photos, bios, project descriptions — sits in plain, consistent containers. The site keeps its personality without depending on non-designers to maintain it.

### Lowercase

- Site chrome is lowercase: nav reads `home / community / contact`; section headings read `next meeting`, `who's in the network`; buttons use plain lowercase verbs.
- **Never apply `text-transform: lowercase` globally.** It flattens member names, film titles, and place names into something that reads as broken. Scope lowercase to nav, headings, labels, and buttons only.
- CMS content renders exactly as typed. If a member enters "Maria Delgado," she stays capitalized. That is correct, not inconsistent.

### Made by hand

- **Asymmetry.** Content column sits offset left, not dead-center. Templates center everything; hand-made things don't.
- **Hand-drawn wordmark.** The FOD logo is a scanned hand-drawn wordmark, not type set in a font. Treat it as an image asset.
- **One signature device, drawn from film-set materials.** Something native to their world — Sharpie-on-gaffer-tape labels, slate handwriting, call-sheet typography — used as a consistent structural element, not scattered decoration. Pick one and commit.
- Spend boldness in one place. The signature device is the memorable thing; everything around it stays quiet and disciplined.

### Avoid

- Centered hero sections
- Generic icon sets
- Cream background + high-contrast serif + terracotta accent
- Near-black background + single bright acid-green or vermilion accent
- Anything that reads as a current template default

### Palette

Derive hex values from an actual photo shot by a group member in the desert, rather than from a swatch generator. 4–6 named values, defined once as CSS custom properties at the top of `style.css`. Everything else references those variables.

## Conventions

- Semantic HTML — real `<nav>`, `<header>`, `<main>`, `<footer>`
- One stylesheet, one nav partial shared by all pages
- Mobile-first CSS; most visitors will be on phones
- Accessible by default: alt text on every image, visible focus states, sufficient contrast, keyboard-navigable
- YouTube embeds should be lazy-loaded (use `loading="lazy"` on the iframe) so the Community page stays fast
- Never hardcode content that belongs in the CMS — if it's something the org might want to change, it's a field

## Behavior Rules

- **Do not invent content.** No placeholder email addresses, member names, meeting locations, or project titles that could get mistaken for real. Use obvious placeholders like `TKTK` and flag them.
- **Do not add dependencies** beyond Eleventy without asking. The dependency count is a maintenance liability for a volunteer org.
- **Do not add a login/members-only area.** The source doc mentions "Member Sign In (?)" with a question mark — it is undecided and out of scope until someone says otherwise.

## Open Questions

Ask before implementing:

- The actual FOD email address
- Where the role signup form lives (Google Form URL, or something else)
- The final list of roles for the dropdown — the current list in `config.yml` is a first guess
- Whether member sign-in is happening at all
- Photo disclosure statement — the exact wording the org wants
- The palette hex values (pending a source photo from the group)
- The hand-drawn wordmark file (pending someone in the group drawing it)
- Which film-set material becomes the signature device
- Body typeface — needs to be plain and readable, and pair against the hand-drawn wordmark
