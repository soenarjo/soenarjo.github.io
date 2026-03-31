# CLAUDE.md - Project Guide

## Project Overview
Personal academic website for Aditya Soenarjo, hosted on GitHub Pages at `soenarjo.github.io`.
Built with Jekyll using the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) remote theme (v4.28.0).

## Architecture
- **No theme source files in repo** — uses `remote_theme` in `_config.yml` to pull Minimal Mistakes from GitHub at build time
- **Skin**: "air" (light gray background, blue accents)
- **Pages**: Home (`index.md`), Papers & Discussions (`_pages/papers.md`), CV (external PDF link)

## Key Files
- `_config.yml` — Site configuration, author info, theme settings, plugin list
- `_data/navigation.yml` — Top navigation menu
- `index.md` — Home page content
- `_pages/papers.md` — Papers & Discussions page
- `assets/images/headshot.jpg` — Author headshot
- `assets/css/main.scss` — Custom CSS overrides (scrollbar fix, dark mode, sidebar sizing)
- `assets/js/dark-mode.js` — Dark mode toggle logic (persists preference via localStorage)
- `_includes/head/custom.html` — Applies saved dark mode class before first paint (avoids flash)
- `_includes/footer/custom.html` — Loads dark-mode.js
- `Gemfile` — Ruby dependencies (`github-pages` gem + `jekyll-include-cache`)

## Local Development
```bash
# Ruby 3.1 is required (rbenv is configured via .ruby-version in the repo root)
# First-time setup:
eval "$(rbenv init -)" && gem install bundler && bundle install

# Serve outside Dropbox folder to avoid Dropbox sync interference:
eval "$(rbenv init -)" && bundle exec jekyll serve --destination /tmp/jekyll-site --no-watch
# Site available at http://127.0.0.1:4000
```

> **Note**: Always use `--destination /tmp/jekyll-site --no-watch` when developing locally.
> The repo lives in a Dropbox-synced folder; without these flags, Dropbox triggers continuous
> Jekyll rebuilds, causing page flicker.
>
> **Ruby version**: Requires Ruby 3.1.x. Ruby 2.7 is too old (nokogiri needs 3.0+) and
> Ruby 3.2+ breaks Jekyll 3.9 (Liquid's `tainted?` removed). A `.ruby-version` file pins 3.1.6
> via rbenv. Install rbenv + ruby-build via Homebrew if needed.
> **Edge case**: If rbenv is installed on a machine but Ruby 3.1.6 is not yet, rbenv will error
> on any `bundle` command. Fix: `rbenv install 3.1.6`. If rbenv is not present, `.ruby-version`
> is silently ignored and the system/rvm Ruby is used instead.

## Deployment
Push to `master` branch via PR. GitHub Pages builds and deploys automatically.

> **Warning**: This repo has an `upstream` remote pointing to `mmistakes/minimal-mistakes`. Always specify `--repo soenarjo/soenarjo.github.io` when creating PRs with `gh pr create`, otherwise the PR may be opened on the upstream theme repo instead.

## Adding/Editing Content
- **New pages**: Add markdown files to `_pages/` with front matter (`layout: single`, `author_profile: true`, `permalink: /your-path/`)
- **Navigation**: Edit `_data/navigation.yml` to add/remove nav links
- **Author info**: Edit the `author:` section in `_config.yml`
- **Theme updates**: Change the version tag in `remote_theme` in `_config.yml`
- **Custom CSS**: Add overrides to `assets/css/main.scss` (imports theme first, then custom rules)
- **Papers page**: Edit `_pages/papers.md` — abstracts use `<details>`/`<summary>` HTML for collapsible sections

## Dark Mode
- Toggle button injected into sidebar via `assets/js/dark-mode.js`
- Preference saved to `localStorage` and applied before first paint via `_includes/head/custom.html`
- CSS lives in the `html.dark-mode { }` block in `assets/css/main.scss`

## Custom Domain
`www.soenarjo.com` is configured via:
1. `CNAME` file containing `www.soenarjo.com`
2. DNS (Squarespace): CNAME `www` -> `soenarjo.github.io`, A records for apex domain to GitHub IPs
3. HTTPS enforced in GitHub repo Settings > Pages
