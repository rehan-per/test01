# Apps & Builds

A collection of standalone apps and builds. Each project lives in its own
top-level directory with its own notes.

## Projects

| Project | What it is | Status |
|---------|------------|--------|
| [sip-the-season-kiosk](sip-the-season-kiosk/) | Single-file touch kiosk for the ETS "Sip The Season" tea range (BIA departure store) — browse categories → ranges → blends, with search, multi-language, and accessibility controls. | Placeholder-asset build; see its [BUILD_NOTES.md](sip-the-season-kiosk/BUILD_NOTES.md) |

## Conventions

- **One directory per project**, named in `kebab-case`.
- Each project is self-contained: source, assets, and a notes/README file describing
  what's final vs. placeholder and any decisions made outside a written spec.
- Prefer no build step where practical (single self-contained HTML files, etc.).

## Claude Code skills

`.claude/skills/` holds design/UI tooling that Claude Code picks up automatically when
working anywhere in this repo:

| Skill | What it does |
|-------|--------------|
| [`ui-ux-pro-max`](.claude/skills/ui-ux-pro-max/) | Searchable local UI/UX database (styles, palettes, font pairings, UX guidelines, per-stack rules) from [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill). Vendored copy — the search script is pure Python 3 stdlib, no install step. Update by re-copying the skill's folder from upstream. |

## Previewing a static project locally

```bash
python3 -m http.server 8731 --directory <project-dir>
```

then open <http://localhost:8731>.

## Deployment

This repo is connected to a Cloudflare **Worker** project named `test01` (Workers & Pages →
Compute, not Pages). `wrangler.jsonc` at the repo root deploys it as a static-assets Worker —
no server-side script, just the `sip-the-season-kiosk/` folder served as-is. Each folder needs
an `index.html` for its root URL to resolve.

Only one project can be wired up to `wrangler.jsonc`'s `assets.directory` at a time. Adding a
second deployable project later means either pointing this file at it instead, or creating a
second Cloudflare project (Worker or Pages) with its own "Root directory" build setting scoped
to that folder.
