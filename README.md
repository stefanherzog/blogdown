# Stefan Herzog's research website

Source for [www.stefanherzog.org](https://www.stefanherzog.org). A static site built with [Hugo](https://gohugo.io/) (the [hugo-apero](https://hugo-apero-docs.netlify.app/) theme), scaffolded as an R [blogdown](https://bookdown.org/yihui/blogdown/) project, and deployed on [Netlify](https://www.netlify.com/).

In practice it's plain Hugo + Markdown — the R/blogdown layer is dormant. Most content is Markdown front matter under `content/`; see [CLAUDE.md](CLAUDE.md) for the architecture in depth.

## Preview locally

Hugo is pinned to `0.101.0`. blogdown keeps that exact binary cached, so you can preview without installing anything:

```
"/Users/herzog/Library/Application Support/Hugo/0.101.0/hugo" server
```

Then open <http://localhost:1313/>. The server live-reloads as you edit. (From RStudio, `blogdown::serve_site()` does the same.)

## How deploys work

- **`master` is production.** Every push to `master` triggers a Netlify build that deploys to www.stefanherzog.org. There is no separate build step to run by hand.
- **Pull requests get a preview.** When you open a PR against `master`, Netlify builds it and posts a unique `deploy-preview-N--stefanherzog.netlify.app` link as a check on the PR. Use it to see your change live before it goes to production.
- **Mistakes are cheap.** Any past deploy can be restored with one click in the Netlify dashboard (Deploys → pick a deploy → Publish).

## Making a change

### Default: most edits

For everyday changes — text, a link, an image, swapping the CV PDF:

1. Preview locally with `hugo server` (see above).
2. Commit and push to `master`:

   ```
   git add -A && git commit -m "describe the change"
   git push
   ```

Netlify deploys it to production automatically. No branch or PR needed. If something looks wrong afterward, restore a previous deploy with one click in Netlify (Deploys → pick a deploy → Publish).

### Optional: bigger or riskier changes

Reach for a branch and a pull request when you want to check a change on a real Netlify build before it goes live — e.g. theme/layout edits, `config.toml` or build-setting changes, a CSS overhaul, anything where local preview might not match production, or when you want a shareable link for feedback. This is [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow):

1. `git checkout master && git pull`
2. `git checkout -b short-descriptive-name`
3. Edit, committing in small logical chunks.
4. `git push -u origin short-descriptive-name`
5. Open a PR against `master` (on GitHub, or `gh pr create`).
6. Open the **Deploy Preview** link Netlify posts on the PR and check it.
7. Merge the PR (*Rebase and merge* or *Create a merge commit* — avoid *Squash* if you want to keep individual commits). Merging deploys to production.
8. Tidy up: `git checkout master && git pull && git branch -d short-descriptive-name`

### When is a preview actually worth it?

Local preview matches production for almost everything. A Netlify preview mainly catches: production-only rendering (minification, analytics, `noindex` — local runs in dev mode), case-sensitive-filename bugs (your Mac is case-insensitive, Netlify's Linux build is not), and whether the Netlify build succeeds at all. For plain content edits these rarely bite, so the default path above is usually fine.

## Repository layout (orientation)

- `content/` — site content as Markdown (most pages are front matter only; the body is rendered by theme layouts). Only **Home**, **About**, and **CV** are live; the rest is dormant example content.
- `config.toml` — site configuration, menus, social links, and `baseURL` (must stay set to the canonical domain).
- `assets/` — SCSS (`custom.scss`, `custom-fonts.scss`) and theme palettes; site-specific styling lives here, not in the theme.
- `themes/hugo-apero/` — the theme (a vendored copy; don't edit it — override via `assets/` and `layouts/`).
- `static/` — files copied verbatim into the site (images, fonts, favicon).
- `netlify.toml` — build command and pinned Hugo version for Netlify.

Generated output (`public/`, `resources/_gen/`, `.hugo_build.lock`) is gitignored — Netlify regenerates it from source on every deploy.
