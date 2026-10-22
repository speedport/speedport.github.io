# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Jekyll site for **speedport.io**, a German-language product-review/recommendation site. It is served by GitHub Pages (custom domain via `CNAME`, HTTPS not enforced).

## Repo state: two themes in flight

- **`master` on GitHub (what is deployed)** still uses the old *jekyll-qck-theme*: `markdown: redcarpet`, `_pages/`, `_layouts/post.html`, and a Gemfile built on the `github-pages` gem. `redcarpet` has to be listed in the Gemfile explicitly, because `github-pages` no longer bundles it. The Umami tracking `<script>` tags live in that theme's `_layouts/default.html`.
- **The local working tree** holds a large, mostly *staged but uncommitted* migration to the **Feeling Responsive** theme (Phlow). It uses kramdown, `pages/`, `_data/*.yml` and Foundation-based `_sass/`. `README.md` is the theme's upstream README, not project docs.

Before committing, check `git status` and `git log HEAD..origin/master`, because the local branch has fallen behind GitHub before. Don't mix deploy fixes into the migration commit. When the migration lands, carry the Umami script tags into `_includes/_head.html`.

## Build & deploy

- Deploy runs through `.github/workflows/jekyll.yml` on every push to `master`, using Ruby 3.1 and `bundle exec jekyll build`. There is no legacy Pages branch build. `Gemfile.lock` is gitignored, so CI resolves gems fresh each time.
- Local dev overlays the dev config: `bundle exec jekyll serve --config _config.yml,_config_dev.yml`. `_config_dev.yml` points `url` at `thalassa.pd320:4000` and turns off analytics.
- `local-dev.sh` / `local-dev-upload.sh` rsync the repo to the host `thalassa:~/dev/speedport`. Serving then runs there in Docker (`bretfisher/jekyll-serve`).
- On this Mac the Homebrew Ruby is 4.x, and Jekyll 3.x / Liquid 4 (pulled in by `github-pages`) will not run on it (`tainted?` errors, missing `csv`). To verify a build, use Ruby 3.1 (Docker `ruby:3.1`) or push and check the Actions run (`gh run list --repo speedport/speedport.github.io`).
- There are no tests or linters. A successful `jekyll build` is the check.

## Content conventions (Feeling Responsive tree)

- Review posts live in `_posts/<topic>/` (e.g. `_posts/kitchen/`). They use `layout: page` and `categories: [reviews]`, with the topic going in `tags`. `pages/reviews.md` lists `site.categories.reviews`, so a post without the `reviews` category won't appear there.
- `_posts/design/` and the "Templates"/"Header Styles" entries in `_data/navigation.yml` are theme demo content that is still around.
- Permalinks are `/:categories/:title/`. Header images are referenced by filename relative to `images/` (`site.urlimg`).
- The navigation, authors, social links and UI strings (`language.yml`, `language_de.yml`) are in `_data/`.
