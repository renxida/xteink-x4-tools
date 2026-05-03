# xteink-x4-tools

Linux-native pipeline for getting books onto a [Xteink X4](https://www.xteink.com/) running
[Papyrix](https://github.com/bigbag/papyrix-reader) firmware as pre-paginated
XTC files, so page turns are instant — no `Indexing...` overlays.

## Companion repos

- **[renxida/calibre-xtc](https://github.com/renxida/calibre-xtc/tree/cedar/main)**
  — fork of the Calibre XTC output plugin with bug fixes (orphan call,
  chapter-index gaps, UTF-8 truncation, `@page` !important to win the EPUB
  cascade), a WeasyPrint renderer that's ~20× faster than the original
  Qt one, and four top-5 typography reports with embedded preview images.
  Pulled in here as a git submodule.
- **[renxida/papyrix-reader](https://github.com/renxida/papyrix-reader/tree/cedar/observed-issues)**
  — fork of the Xteink X4 firmware with a `NOTES.cedar.md` covering two
  real bugs hit while running v1.20.3 (first-boot state-machine wedge
  without SD card; "Indexing..." overlay on every EPUB page turn).

## Quick start

```
bin/epub2xtc input.epub output.xtc
```

Wraps `ebook-convert` with the WeasyPrint renderer (sync, no Qt, no GPU; ~20×
faster than the original Qt path on real books).

## Layout

- `bin/` — wrapper scripts: `epub2xtc`, `convert-library`, `run-variants`,
  `build-reports`, `render-top5-full`
- `calibre-xtc/` — our fork of the Calibre plugin (branch `cedar/main`)
- `samples/` — EPUB sources + generated XTCs
  - `samples/test_corpus/` — files staged for SD-card load (see its README)
  - `samples/variants/` — typography sweep outputs
- `sdcard-backup/` — pre-wipe tarball of the original SD contents
- `logs/LOG.md` — session journal; resume here (newest first)
- `refs/` — saved upstream sources (just snapshots, not editable)

## Where to look

In the fork (`renxida/calibre-xtc` branch `cedar/main`):

- [INDEX.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/INDEX.md) — entry point
- [TOP5_PORTRAIT.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/TOP5_PORTRAIT.md) — five serif picks for portrait
- [TOP5_LANDSCAPE.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/TOP5_LANDSCAPE.md) — same five, calibrated landscape
- [TOP5_LANDSCAPE_SANS.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/TOP5_LANDSCAPE_SANS.md) — five sans-serif picks (Atkinson Hyperlegible leads)
- [TOP5_LANDSCAPE_MONO.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/TOP5_LANDSCAPE_MONO.md) — five monospaced picks (Iosevka, 0xProto are Berkeley-Mono-ish)
- [VARIANTS.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/VARIANTS.md) — every variant tried
- [DITHERING.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/DITHERING.md) — Floyd-Steinberg vs threshold
- [NOTES.cedar.md](https://github.com/renxida/calibre-xtc/blob/cedar/main/NOTES.cedar.md) — env-var reference + fork-vs-upstream changes

In this repo:

- [logs/LOG.md](logs/LOG.md) — chronological project journal (newest first)
