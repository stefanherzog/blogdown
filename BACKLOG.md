# Backlog

Task backlog for the site. Grouped by horizon — **Now** (do soon / quick wins), **Next** (on deck), **Later** (deferred or needs a decision first). Checkboxes are standard GitHub task syntax (`- [ ]` open, `- [x]` done). Size tags: `[S]` quick, `[M]` moderate, `[L]` substantial.

See [README.md](README.md) for the edit/preview/deploy workflow and [CLAUDE.md](CLAUDE.md) for architecture.

## Now

- [ ] **Disable GitHub Pages build/deploy** `[S]` — The repo's "pages build and deployment" GitHub Actions workflow runs and **fails** (it can't build this Hugo-on-Netlify source), and emails failure notices. GitHub Pages is unused (Netlify is the sole deploy target). Turn it off: repo **Settings → Pages → Source: None**.
- [ ] **Fix terminal/CLI `git push`** `[M]` — Pushes currently only work via the **Tower** GUI client. The remote is HTTPS (`https://stefanherzog@github.com/stefanherzog/blogdown.git`) with no stored credential, and the embedded/Zed shell is non-interactive so git can't prompt (`could not read Password … Device not configured`). SSH also fails — `ssh -T git@github.com` → `Permission denied (publickey)`, i.e. the local `~/.ssh/id_ed25519` key isn't registered with GitHub (or not loaded in the agent). Fix: (a) add the SSH public key to GitHub and `git remote set-url origin git@github.com:stefanherzog/blogdown.git`; or (b) configure an HTTPS credential — store a PAT in the `osxkeychain` helper (already configured), or install `gh` then `gh auth login` + `gh auth setup-git`.
- [ ] **Disable Netlify "Legacy Prerendering"** `[S]` — Enabled but deprecated by Netlify. Harmless to remove for a static Hugo site (prerendering only matters for client-rendered JS apps). Netlify dashboard → it prompts to disable via *Configure*.
- [ ] **Bump Netlify build Node.js 16.x → 24.x LTS** `[S]` — Node 16 is end-of-life. The Hugo build almost certainly doesn't use Node (SCSS compiles via Hugo's built-in extended libsass, no PostCSS/npm), so this is low-risk housekeeping. Prefer pinning **in-repo** for reproducibility: add `NODE_VERSION` under `[build.environment]` in `netlify.toml` (next to `HUGO_VERSION`) or a `.nvmrc`, rather than only the dashboard.

## Next

- [ ] **Add privacy-friendly analytics** `[M]` — None currently (`googleAnalytics` empty in `config.toml`). Want a **European / GDPR-reasonable, ideally cookieless** option (avoid Google Analytics). Candidates: Plausible (EU-hosted, OSS), Simple Analytics (NL), Pirsch (DE), Matomo (OSS, self-host or EU cloud), self-hosted Umami/GoatCounter. Integrate via a `head` partial override in `layouts/` (don't edit the vendored theme).
- [ ] **Evaluate upgrading Hugo from `0.101.0`** `[M/L]` — The pin (mid-2022) is neglect, not deliberate. Check what changed since 0.101.0 and whether hugo-apero still builds, then bump `HUGO_VERSION` in `netlify.toml` and `.Rprofile`. Test the theme on a branch/preview before merging.

## Later

- [ ] **Replace the generic social-share image** `[S]` — `og:image`/`twitter:image` point at `static/img/robots.jpg` (a generic robot illustration), set via `sharing_image` in `config.toml`. It's the thumbnail shown when the site is shared (Bluesky/LinkedIn/Twitter). Consider a photo of Stefan or a research-themed image: drop it in `static/img/` and update `sharing_image`. Discretionary.
- [ ] **Decide which demo sections go live** `[L]` — `blog` / `talks` / `projects` / `collection` are dormant hugo-apero exampleSite content (menus commented out in `config.toml`). Decide which to activate. Each still carries template meta descriptions that must be fixed on activation (see CLAUDE.md → Where content lives).
- [ ] **Inventory and resolve legacy drift** `[M]` — Deliberate pass to decide what to keep, rename, or remove from the repo's blogdown/GitHub-Pages origins. Known items: GitHub repo named `blogdown` and local folder named `stefanherzog.github.io` are both misnomers — rename for clarity? Vestigial R/blogdown scaffolding (`index.Rmd`, empty `R/build.R` & `R/build2.R`, `.Rprofile`, `.Rproj`) no longer runs (editing is in Zed, the build is plain Hugo) — remove if R/RStudio is truly unused? Two drift items are tracked separately and not duplicated here: the failing GitHub Pages deploy (Now → "Disable GitHub Pages build") and dormant demo content (Later → "Decide which demo sections go live"). Meanwhile, don't trust the repo's surface names/structure.

## Done

- [x] 2026-06-30 — Fix `baseURL` to canonical domain `www.stefanherzog.org` (was the Netlify default; fixed canonical/`og:url`/sitemap). Verified live.
- [x] 2026-06-30 — Replace template placeholder meta description on the About page.
- [x] 2026-06-30 — Add cross-platform font fallback stack (Avenir-led, ends in `sans-serif`).
- [x] 2026-06-30 — Untrack and gitignore Hugo generated artifacts (`public/`, `resources/_gen/`, `.hugo_build.lock`).
- [x] 2026-06-30 — Adopt GitHub Flow workflow; add `README.md` and `CLAUDE.md`.
