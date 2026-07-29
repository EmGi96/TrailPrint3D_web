# AGENTS.md

Guidance for AI agents working in this repo.

## What this repo is

The marketing/docs website for the **TrailPrint3D** Blender add-on. Static Jekyll
site, deployed via GitHub Pages to `trailprint3d.com` (see `CNAME`). No build
tooling beyond stock GitHub Pages Jekyll — don't add plugins, npm build steps,
or bundlers that GitHub Pages' default Jekyll build won't process.

The add-on's actual source code lives in a separate repo:
`https://github.com/EmGi96/TrailPrint3D`. This repo only contains the website.

## Changelog updates

`changelog.html` is a hand-curated copy of the add-on's changelog, **not** a
literal mirror. Pull updates from:

```
https://raw.githubusercontent.com/EmGi96/TrailPrint3D/main/CHANGELOG
```

(human-browsable at `https://github.com/EmGi96/TrailPrint3D/blob/main/CHANGELOG`)

When a version in the raw file isn't yet reflected in `changelog.html`, add it
following the existing house style rather than pasting the raw text verbatim:

- Every bullet gets a prefix: `Added:`, `Changed:`, `Fixed:`, or `Removed:`
  (the raw file sometimes omits these — infer the right one).
- Sentence casing, not Title Case (`"Improved river generation"`, not
  `"Improved River Generation"`) — except for proper nouns/feature names
  (`Single Color Mode`, `Mapterhorn`, `GeoJSON`, `Puzzle Configurator`, ...).
- Fix obvious typos from the raw source (e.g. `buidlings` → `buildings`,
  `diffrent` → `different`).
- Use an em dash (`—`) instead of a period or `->` to attach an elaboration
  to a bullet.
- Drop contributor credits like `(by X)`, `(requested by @X)`,
  `(thanks to @X)` by default — not generally used on this site. Only keep
  one if the user explicitly asks to credit someone for a specific entry
  (e.g. `— by Louisefeige` on the GeoJSON boundary import bullet).
- An unreleased version at the top of the raw file is usually dated
  `Upcoming` — use that literal string as the `.cl-date` text instead of a
  date.

Structurally, each version is a `.cl-block` with:
- `id="v{major}-{minor-with-dots-as-dashes}"`, e.g. `3.1.1` → `v3-1-1`,
  `3.09` → `v3-09`.
- A header `div.cl-header` with `onclick="toggleBlock('<id>')"` matching the
  block id.
- A `.cl-body > ul > li` per entry.

New versions are inserted in reverse-chronological order (newest at the top,
right after `<h1>Changelog</h1>`). Copy an existing block as a template rather
than writing one from scratch — the accordion script
(`toggleBlock`/`openFromHash` at the bottom of `changelog.html`) depends on
every `.cl-block` having a unique `id` and relies on `.cl-block` /`.cl-body`
class names from `_includes/styles-changelog.html`.

**Also check `_layouts/default.html`** (~line 46): the homepage release
banner (`<span>01.07.2026 - Version 3.1.1 released</span>`) is a separate,
manually-maintained string. It should only be bumped when a version actually
ships (not for `Upcoming`/unreleased entries), and is easy to forget since
it's not near `changelog.html`.

## Highlighting Premium features

TrailPrint3D has a free and a Premium (Patreon) tier. Whenever content
describes a feature that is Premium-only, flag it — don't let it read as
available to everyone. The site's established visual language for this is
gold/amber (`rgba(234,179,8,...)`), consistently used in `styles.css` via
`.feat-tier-badge.premium` and `.feat-card-premium`. Follow the pattern
already used on the page you're editing rather than inventing a new one:

- **`changelog.html`**: append a `<span class="cl-premium-tag">Premium</span>`
  badge (gold pill, styled in `_includes/styles-changelog.html`) to the
  bullet, e.g.
  `Added: Shell geometry for text-based shapes <span class="cl-premium-tag">Premium</span>`.
- **`howto.html`**: prefix the list item with `<strong>Premium</strong>: `,
  e.g. `<li><strong>Premium</strong>: Pin on City lets you drop a pin by
  typing a city name instead of coordinates</li>`.
- **Feature/pricing cards** (`index.html`, `download.html`): use the existing
  `.feat-tier-badge.premium` / `.feat-card-premium` classes rather than
  ad-hoc styling.

If a page has no existing convention for marking Premium content, don't
silently skip it — call it out to the user and propose one instead of
guessing.

## Page structure

Pages are `.html` files with Jekyll front matter at the top:

```yaml
---
layout: default
title: "Page Title — TrailPrint3D"
description: "..."
canonical: "https://www.trailprint3d.com/page.html"
nav_active: <matches a nav link in _layouts/default.html, e.g. changelog>
page_styles: <optional — see below>
---
```

`_layouts/default.html` is the single shared layout (nav, footer, cookie
banner, analytics consent gating). Per-page CSS lives in
`_includes/styles-<name>.html` and is pulled in via an
`{% raw %}{% if page.page_styles == '<name>' %}{% endraw %}` conditional in
`_layouts/default.html`'s `<head>`. If you add a new page that needs its own
styles, you must add both the include file *and* the conditional line —
there's no automatic wiring.

## Conventions

- No JS framework/build step — vanilla HTML/CSS/JS only, inline `<script>`
  blocks per page (see `changelog.html`, `howto.html`).
- `howto.html` and `changelog.html` share the same accordion/`.cl-*` CSS
  classes and toggle script — keep them in sync if you change the pattern in
  one (e.g. the single-open-at-a-time + shareable URL-hash behavior).
- Analytics (GA4, `G-YMJ4P3S5R6`) only loads after explicit cookie consent —
  don't add tracking scripts that bypass the consent gate in
  `_layouts/default.html`.
- No test suite. Verify HTML/CSS/JS changes by opening the page directly and
  exercising the interaction (accordions, dropdowns, cookie banner), and
  check the mobile breakpoint in the relevant `styles-*.html` file
  (typically a `@media (max-width: 560px)` block).
