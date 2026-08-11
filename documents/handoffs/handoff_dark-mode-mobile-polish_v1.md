# Handoff: Promote remittances paper, self-host all PDFs, and add Fed disclaimer

**Task:** `dark-mode-mobile-polish`
**Version:** v1
**Date:** 2026-08-11
**Timestamp:** 2026-08-11 17:23
**Session:** 2024415b-5ce1-4639-9e9a-c498f8e6dc5d
**Model:** Claude Opus 5
**Author:** Adi Soenarjo <adityasoenarjo@gmail.com>

---

## Purpose

Three related goals on the public academic site:

1. **Promote "The Macroeconomics of International Remittance Flows"** from *Work in
   Progress* to *Working Papers*, now that a circulating draft exists — with a new
   abstract, download buttons, and a coauthor link.
2. **Stop depending on nbviewer.** Every paper, slide deck, and the CV were previously
   served through `nbviewer.org` proxying a *separate* repo (`soenarjo/website`).
   That chain has four external failure points; all PDFs are now hosted in this repo.
3. **Add the required Federal Reserve disclaimer** to the site footer.

A canonical-URL misconfiguration was found and fixed along the way.

> **Note on the task name:** it is taken from the git branch, which predates this
> session's work. Nothing here relates to dark mode or mobile polish specifically,
> though changes were verified in both themes and at phone width.

## What Changed

**`_pages/papers.md`** — the bulk of the work:

- Moved the remittances paper to the top of `## Working Papers`, above *Liquidity and
  Labor Reallocation*, structured like the existing GVC entry.
- Replaced its abstract with the new four-result + two-country-HANK text, split into two
  `<p>` blocks. Fixed PDF line-wrap hyphenation artifacts carried in from the paste
  (`in- dividuals`, `pri- vate`, `Lever- aging`, `sender- currency`, `en- dogenous`)
  while preserving genuine hyphens. Used `&minus;` and `&mdash;` entities.
- Emptied the `## Work in Progress` section (heading and separator retained).
- Linked coauthor M. Ludovica Ambrosino to her Google Sites page.
- Renamed all three `PDF` buttons to `Paper`; left the `Discussion` button unchanged.
- Added an `Appendix` button to the remittances entry, positioned between Paper and
  Alternate.
- Changed the Liquidity status line from *New version forthcoming.* to *June 2025.*
- Repointed every button at `/assets/pdf/…` and **removed all three `Alternate`
  buttons** (see Design Decisions).

**`assets/pdf/`** (new, untracked before this session) — six PDFs, 12 MB total:
`remittances.pdf`, `remittances_appendix.pdf`, `reallocation.pdf`, `gvcs.pdf`,
`discussion_Florio_Siena_Zago.pdf`, `CV_Soenarjo.pdf`.

**`_includes/footer/custom.html`** — added the disclaimer div:

```html
<div class="page__footer-disclaimer">Disclaimer: The views expressed here are my own
and do not necessarily represent the views of the Federal Reserve Bank of Boston or
the Federal Reserve System.</div>
```

**`assets/css/main.scss`** — a `#footer > footer` flex block ordering the disclaimer
below the theme's copyright line, plus `.page__footer-disclaimer` added to the existing
dark-mode footer color rule.

**`_config.yml`** — sidebar CV link repointed to `/assets/pdf/CV_Soenarjo.pdf`;
`url:` corrected from `https://soenarjo.github.io` to `https://www.soenarjo.com`.

**`_data/navigation.yml`** — top-nav CV link repointed to the local path.

## Inputs / Outputs

**Inputs**
- Six PDFs placed by the user in `assets/pdf/` (largest: `reallocation.pdf` 4.9 MB,
  `remittances_appendix.pdf` 4.6 MB).
- New abstract text and the coauthor's URL, supplied in-session.

**Outputs**
- Static site built by Jekyll. Each PDF gets a permanent public URL of the form
  `https://www.soenarjo.com/assets/pdf/<filename>.pdf`.

**Local build** (per project CLAUDE.md — destination outside Dropbox, no watch):

```bash
eval "$(rbenv init -)" && bundle exec jekyll serve --destination /tmp/jekyll-site --no-watch
```

Note: a `bundle install` was required this session — `commonmarker`, `racc`,
`eventmachine`, `http_parser.rb`, `json`, and `bigdecimal` were missing locally.

## Design Decisions

**Self-hosting over nbviewer.** The old chain depended on nbviewer staying alive, the
`website` repo keeping its name, the branch staying `main`, and GitHub's blob URL
format. The new URLs depend only on the domain and the file path. Jekyll copies
`assets/pdf/` verbatim — `_config.yml`'s `exclude:` list needed no change.

**`assets/pdf/` as the location.** Mirrors the existing `assets/css`, `assets/js`,
`assets/images` convention and confines every binary to one directory.

**Unversioned filenames.** PDFs are overwritten in place on revision rather than
suffixed with dates or version numbers, so URLs already circulating in referee emails
and paper title pages keep resolving to the current draft.

**Alternate buttons removed.** They existed solely as a fallback for nbviewer failing
to render. With local hosting they would point at the same file via an external repo
being migrated away from. User confirmed removal.

**Flex ordering for the footer disclaimer.** Minimal Mistakes renders
`_includes/footer/custom.html` *before* its copyright div, so without `order` the
disclaimer would sit above "Powered by Jekyll". The alternative — copying the theme's
`footer.html` into the repo — would break the deliberate "no theme source files in
repo" arrangement documented in CLAUDE.md. A `margin-top` was tried and removed: in
dark mode `.page__footer-copyright` carries a darker background panel, and the gap
split it into two detached bands.

**Repo weight accepted.** 12 MB against GitHub's 1 GB soft limit; at realistic
revision rates (~33 MB/year) that is decades of headroom. The global "never commit
large files" rule targets research data repos, where a committed `.dta` is a mistake —
here the PDFs are the deliverable.

## Known Limitations and TODOs

- **The PDFs' own "Latest Version" links still point at nbviewer.** Visible on the
  remittances title page. These live in the LaTeX source, not the site. Update to
  `https://www.soenarjo.com/assets/pdf/<name>.pdf` (or to `/papers/`, which survives
  a rename and shows abstract + appendix + R&R status) **after** this branch merges —
  the URLs 404 until Pages deploys.
- **`## Work in Progress` is an empty heading** followed by a horizontal rule. Renders
  as a visible gap, more noticeably on mobile. Either populate it or comment out the
  heading.
- **CDN caching**: overwriting a PDF at a stable path can serve stale copies for a
  while. Hard-refresh (Cmd+Shift+R) to verify after deploy.
- **`remittances_appendix.pdf` (4.6 MB) and `reallocation.pdf` (4.9 MB)** dominate repo
  growth; each revision commits a full new copy. Compressing embedded figures before
  committing is worth the habit if revisions become frequent.
- Dark-mode footer text sits on a slightly darker panel than the surrounding footer
  (`#0d1b2a` vs `#112240`) — pre-existing, matched rather than fixed.

## Verification Performed

- Full Jekyll build clean on every change.
- All six PDF URLs return `200 application/pdf`; SHA-256 checksums match the source
  files byte for byte.
- Every PDF in `assets/pdf/` confirmed referenced by `_pages/papers.md`, `_config.yml`,
  or `_data/navigation.yml` — no orphans.
- Zero `nbviewer` or `soenarjo/website` references remain in source.
- Canonical tag and `og:url` now emit `https://www.soenarjo.com/…`; zero
  `soenarjo.github.io` strings remain in built HTML or XML.
- Click-through tested in Chrome: nav CV, sidebar CV, and the remittances Paper button
  all open the correct document in the browser's native viewer.
- Rendering checked in dark mode, light mode, and at 414 px phone width.

## Change Log

| Version | Date       | Summary                                                                 |
|---------|------------|-------------------------------------------------------------------------|
| v1      | 2026-08-11 | Promote remittances paper; self-host all PDFs off nbviewer; footer disclaimer; canonical URL fix |
