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

## Previewing a static project locally

```bash
python3 -m http.server 8731 --directory <project-dir>
```

then open <http://localhost:8731>.
