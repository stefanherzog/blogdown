# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Stefan Herzog's personal academic research website. A static site built with **Hugo** (pinned to `0.101.0`) using the **hugo-apero** theme, scaffolded as an R **blogdown** project. Deployed to **Netlify**; the canonical production URL is **`https://www.stefanherzog.org`** (apex `stefanherzog.org` redirects to it, and `stefanherzog.netlify.app` is the Netlify default subdomain). The GitHub repo is **`stefanherzog/blogdown`** (private); the local working-directory name `stefanherzog.github.io` is a legacy misnomer — GitHub Pages is not used, Netlify is the sole deploy target. Production canonical/`og:url`/sitemap URLs come from `baseURL` in `config.toml`, so it must stay set to the canonical custom domain.

In practice the site is plain Hugo + Markdown. The blogdown/R layer is vestigial: `index.Rmd`, `R/build.R`, and `R/build2.R` are empty template stubs, and `config.toml` sets `ignoreFiles` to skip `.Rmd`/`.Rmarkdown`. No R code actually runs as part of a normal edit. Treat content edits as Markdown front-matter changes, not R work.

## Build & preview

- Netlify builds production from the **`master`** branch with the bare command `hugo` (see `netlify.toml`); pushing to `master` triggers the production deploy. Branch/preview deploys use `hugo -F -b $DEPLOY_PRIME_URL` (`-F` builds future-dated content).
- Local preview without installing anything globally: blogdown caches the pinned binary at `~/Library/Application Support/Hugo/0.101.0/hugo` — run `"…/0.101.0/hugo" server` from the repo root (it's the `+extended` build, so it compiles the SCSS).
- Local preview: `hugo server` (or from R/RStudio, `blogdown::serve_site()`). Match Hugo `0.101.0` — newer Hugo versions can break this theme.
- The `0.101.0` pin (mid-2022, set in `netlify.toml` and `.Rprofile`) is **frozen by neglect, not a deliberate lock**. Evaluating an upgrade (what changed since 0.101.0, whether hugo-apero still works) is a planned task — upgrading is fair game, just do it deliberately and test the theme.
- `public/` is empty and not the deploy artifact; Hugo regenerates output at build time. Do not hand-edit `public/`.
- **Generated artifacts are gitignored, not committed**: `public/` (rendered HTML), `resources/_gen/` (Hugo's compiled-SCSS cache), and `.hugo_build.lock`. Netlify rebuilds everything from source on each `master` push, so never re-commit these. Corollary: a CSS/font change in `config.toml` or `assets/*.scss` takes effect from source — you don't need to (and shouldn't) commit the regenerated `resources/_gen/` cache.

## Git workflow

**`master` is production** — every push to it auto-deploys via Netlify, and any past deploy can be restored with one click, so mistakes are cheap.

- **Default (most changes):** preview locally (`hugo server`), commit, push straight to `master`. This is the right path for content edits — text, links, images, the CV PDF — where a local preview is enough.
- **Bigger/riskier changes** (theme/layout, `config.toml` or build settings, CSS, case-sensitive-filename risks, or when a shareable preview helps): use GitHub Flow — branch off `master`, push, open a PR, review the Netlify **Deploy Preview** (`deploy-preview-<n>--stefanherzog.netlify.app`), then merge. Delete the branch after merge. No long-lived `dev`/staging branch.

A Netlify preview catches what local `hugo server` can't: production-environment rendering (`HUGO_ENV=production` gates minification/analytics/`noindex`), case-sensitive-filename bugs (macOS is case-insensitive, Netlify's Linux build is not), and whether the build actually succeeds. Note the preview build overrides `baseURL` with its own URL (`hugo -b $DEPLOY_PRIME_URL`), so production canonical/`og:url` output only appears on the real `master` build, never in a preview. Never force-push or amend a commit already on a pushed/open PR branch.

## Where content lives

All editable content is Markdown in `content/`. Most pages are **front-matter only** — the body is rendered by theme layouts, and the files literally end with a note pointing at the relevant layout (e.g. `about/list.html`). Don't expect prose in the body of `_index.md` files.

Active, real pages (only these three are enabled in the `[menu.header]` nav in `config.toml`):

- `content/_index.md` — homepage (front matter: description, action link, hero image)
- `content/about/` — split into `header/`, `main/`, and `sidebar/` index files, each a separate front-matter fragment composed into one page
- `content/cv/` — see below

Everything else in `content/` (`blog/`, `project/`, `talk/`, `collection/`, `elements/`, `form/`) is currently **leftover demo content from the hugo-apero exampleSite**, with menu entries commented out in `config.toml`. These are not slated for deletion: the site is under active development and the intent is to activate some of these sections (which ones is still TBD as of mid-2026). Until then they hold placeholder text and should not be treated as authoritative.

Leftover placeholder text from the template is a known hazard — check before assuming any `_index.md` field is real. Two confirmed cases as of this writing: (1) `content/about/header/index.md` still says "I'm a Hugo theme you'll want to hang out with", but it is **not rendered** because `content/about/_index.md` sets `show_header: false`. (2) The `description:` in `content/about/_index.md` still reads "A website template for Hugo developed by RStudio & Formspree…", and this one **does leak** — `partials/meta.html` feeds `.Params.description` into the page's `<meta name="description">` and `og:description`. Front-matter `description` fields drive head/SEO/social meta even when nothing shows on the page; audit them when activating any section.

## The CV

`content/cv/index.md` just embeds `content/cv/cv-herzog-stefan.pdf` in an iframe. The PDF is maintained in a **macOS Pages document** (kept outside this repo) and exported to PDF by hand — there is no CV source in the repository. The overwhelming majority of commits are "update cv" = replacing that one PDF. To update the CV, replace the PDF file and commit.

## Styling

- Theme palette is selected via `params.theme = "grayscale"` in `config.toml`; the built-in palettes live in `assets/theme/`.
- Site-specific overrides: `assets/custom.scss` (small) and `assets/custom-fonts.scss` (`@font-face` blocks for the bundled webfonts). The active text/heading font is set by `customtextFontFamily`/`customheadingFontFamily` in `config.toml` (currently an Avenir-led stack with cross-platform fallbacks ending in `sans-serif`), injected into SCSS via `assets/scaffold.scss`. Prefer editing these over touching `themes/hugo-apero/`. Note Avenir is Apple-only; non-Apple devices land on the fallbacks.
- `themes/hugo-apero/` is a **vendored copy** of the upstream theme (not a git submodule). It is committed directly. Avoid editing it; overrides belong in the project's `assets/` and `layouts/`. The only project-level layout override is `layouts/shortcodes/blogdown/postref.html`.

## Conventions

- Indentation: 2 spaces (`.editorconfig`, `.Rproj`).
- `config.toml` allows raw HTML in Markdown (`markup.goldmark.renderer.unsafe = true`); the content makes heavy use of inline `<a target="_blank">` links. Match that style.
- Math renders via KaTeX; emoji shortcodes are enabled.

## Planned work

The site is under active development (as of mid-2026). Open tasks:

- **Add privacy-friendly analytics.** No analytics currently (`googleAnalytics` is empty in `config.toml`). Requirement: a **European / GDPR-reasonable, ideally cookieless** option — avoid Google Analytics. Starting candidates to evaluate: Plausible (EU-hosted, OSS), Simple Analytics (Netherlands), Pirsch (Germany), Matomo (OSS, self-host or EU cloud), or self-hosted Umami/GoatCounter. Integration is a script snippet injected via a `head` partial override in `layouts/` (don't edit the vendored theme).
- **Evaluate upgrading Hugo from `0.101.0`.** See Build & preview above — the pin is neglect, not deliberate; check what changed since 0.101.0 and whether hugo-apero still works.
- **Decide which demo sections go live** (blog / talks / projects / collection). They're currently dormant exampleSite content; each still carries template meta descriptions that need fixing when activated (see Where content lives).
- **Disable Netlify "Legacy Prerendering."** It is enabled for this site but Netlify has deprecated it; switch it off (Netlify dashboard → it prompts to disable via *Configure*) and move to the newer prerender extension if prerendering is still wanted. This is a Netlify dashboard setting, not in this repo's config.
- **Bump the Netlify build Node.js version.** The build environment is pinned to Node 16.x, which is end-of-life (Netlify recommends 24.x LTS). The Hugo build almost certainly doesn't use Node (SCSS is compiled by Hugo's built-in extended libsass, no PostCSS/npm pipeline), so this is low-risk housekeeping to clear the EOL warning. Prefer pinning it **in-repo** for reproducibility — add `NODE_VERSION` under `[build.environment]` in `netlify.toml` (alongside `HUGO_VERSION`) or a `.nvmrc` — rather than only setting it in the dashboard.
