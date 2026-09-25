# larakarki.com

Jekyll site styled with Tailwind CSS v4.

## Setup

```sh
bundle install   # Ruby 3.3.6 (see .ruby-version)
npm install      # Node 22 (see .nvmrc)
```

## Develop

```sh
npm run dev                # Tailwind watcher + bundle exec jekyll serve --livereload
bundle exec jekyll serve   # Jekyll only; uses the committed assets/css/main.css
```

Edit styles in `assets/css/tailwind.css`, then run `npm run build:css` (or keep `npm run dev` running) to regenerate `assets/css/main.css`.

## Deploy

See [DEPLOY.md](DEPLOY.md) for GitHub Pages and custom domain setup.

## Content

- Site title, tagline, nav, contact links, footer: `_config.yml`
- Intro and contact copy: `index.html`
- Research areas: one Markdown file per area in `_research/`. `order` in the front matter sets position; the body is a description paragraph followed by a bulleted list of citations.
