# xtc-tools

Linux-native pipeline for getting books onto a Xteink X4 (running
[Papyrix](https://github.com/bigbag/papyrix-reader) firmware) as pre-paginated
XTC files, so page turns are instant. The hard work happens in the
[`calibre-xtc`](calibre-xtc/) fork of `thirteen37/calibre-xtc`.

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

- Reading the typography variants: [`calibre-xtc/VARIANTS.md`](calibre-xtc/VARIANTS.md)
- Picking a font for actual reading: [`calibre-xtc/TOP5_PORTRAIT.md`](calibre-xtc/TOP5_PORTRAIT.md)
- Fork-specific changes vs upstream: [`calibre-xtc/NOTES.cedar.md`](calibre-xtc/NOTES.cedar.md)
- Files to put on the SD card: [`samples/test_corpus/README.md`](samples/test_corpus/README.md)
- What just happened: [`logs/LOG.md`](logs/LOG.md)
