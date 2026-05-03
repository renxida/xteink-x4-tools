# Project Log

Newest first. Each entry: timestamp, what was done, what's next.

---

## 2026-05-02 12:10 — Expanded to 4 top-5 reports

User came back, asked for landscape variants and apples-to-apples sizing across fonts.

**Per-font calibrated zoom** (full PoG should land at ~1100 pages regardless of font) — replaces the naive "same em" approach. Implemented via `--xtc-zoom` per variant. Different intrinsic letter widths converged from 257-page spread to 18-page spread.

**Four top-5 reports:**

| File | Set |
|---|---|
| `TOP5_PORTRAIT.md` | 5 serif fonts in portrait |
| `TOP5_LANDSCAPE.md` | same 5 serifs, calibrated landscape |
| `TOP5_LANDSCAPE_SANS.md` | 5 sans-serif body fonts (Atkinson Hyperlegible standout) |
| `TOP5_LANDSCAPE_MONO.md` | 5 monospaced fonts (Iosevka / 0xProto are the Berkeley-Mono-ish picks) |

Plus `DITHERING.md` (Floyd-Steinberg vs threshold), `VARIANTS.md` (~50 entries across all categories).

**Fonts added in this iteration:**
- Serif portrait set: EB Garamond, Lora, Vollkorn, Source Serif 4, Literata, plus secondary Crimson Pro, Spectral, Gentium, Latin Modern Roman
- Sans: Atkinson Hyperlegible Next, IBM Plex Sans, Inter, Source Sans 3, Noto Sans
- Mono: JetBrains Mono, Iosevka, 0xProto, IBM Plex Mono, Cascadia Code, Monaspace Argon, Monaspace Neon, Maple Mono

Total: 25+ unique font families, all installed in `~/.local/share/fonts/`.

**Tooling improvements:**
- `bin/run-variants` accepts a positional substring filter (`bin/run-variants landscape-mono`)
- Preserves untouched results when filtering, so iteration is fast (4–5s per variant)
- `build-reports` rotates landscape PNGs 90° CCW so they read naturally in a flat viewer
- `epub2xtc --help` shows the env-var knob list

**On-device pack** (`samples/test_corpus/`): 9 full-PoG XTCs (6 portrait fonts + 3 landscape fonts) + format coverage + dithering comparison. ~290 MB.

**WeasyPrint typography knobs (env vars):**
`XTC_FONT_FAMILY`, `XTC_TEXT_ALIGN`, `XTC_HYPHENS`, `XTC_LINE_HEIGHT`, `XTC_DROPCAP`, `XTC_SUPERSAMPLE`, `XTC_FONT_SIZE_ADJUST`, `XTC_EXTRA_CSS`. Documented in NOTES.cedar.md.

---

## 2026-05-02 01:50 — Stable resting point

State of play (commits since branching from upstream `calibre-xtc` v1.0.1):

- 15 commits on `cedar/main`: 3 upstream-able bug fixes, WeasyPrint renderer rewrite, typography knobs, drop cap, Qt-path delay tunables, test fixes, two preview reports.
- All 11 unit tests pass (`calibre-debug -e tests/test_xtc_encoder.py`).
- All 8 staged on-device XTCs validate (`tests/verify_xtc.py`).
- 36 typography variants previewed in [`calibre-xtc/VARIANTS.md`](../calibre-xtc/VARIANTS.md), 5 picks curated in [`calibre-xtc/TOP5_PORTRAIT.md`](../calibre-xtc/TOP5_PORTRAIT.md).
- Test corpus staged at `samples/test_corpus/` with format-coverage source files plus six full-PoG reading candidates (96 MB total).

**Suggested next moves when user is back:**

1. Pull SD card → I'll wipe and copy `samples/test_corpus/*` → reinsert into X4.
2. Try each `full-PoG-*.xtc` for a chapter; pick a font.
3. If you want to push the fork to GitHub, share the repo name; I'll add it as `origin` and push `cedar/main`.
4. The three upstream-able fixes (orphan call, chapter gaps, UTF-8 truncation) are clean and self-contained — straightforward PRs to `thirteen37/calibre-xtc` if you want to upstream them.

---

## 2026-05-02 01:40 — Typography sweep + drop cap

User wanted "all fonts tried" + "5 best portrait" markdown reports + per-variant XTCs they can flash. Did all of it.

**Tooling:**

- `bin/run-variants` sweeps font / setting / combo / renderer / orientation knobs against PoG (`--xtc-max-pages 20` so each takes ~2s)
- `bin/build-reports` compiles `samples/variants/*` into `calibre-xtc/VARIANTS.md` and `calibre-xtc/TOP5_PORTRAIT.md`, mirroring images into `calibre-xtc/preview-images/variants/` so they render on GitHub
- `bin/render-top5-full` produces full PoG XTCs in each top-5 font for actual on-device reading

**WeasyPrint typography knobs added** (env vars; see [`calibre-xtc/NOTES.cedar.md`](../calibre-xtc/NOTES.cedar.md) for the full table):

- `XTC_FONT_FAMILY` — CSS font family
- `XTC_TEXT_ALIGN` — `justify` / `left`
- `XTC_HYPHENS` — `auto` / `manual`
- `XTC_LINE_HEIGHT`
- `XTC_DROPCAP` — chapter-opener 3em initial (regex-wraps first letter of first `<p>` in `<span class="xtc-initial">`; sidesteps a WeasyPrint `::first-letter` + float assertion failure)
- `XTC_SUPERSAMPLE` — 2× pre-dither (negligible visible diff)
- `XTC_EXTRA_CSS` — escape hatch

**Top 5 portrait fonts** (after eyeballing 10 serifs + 3 sans):

1. EB Garamond — classical, refined
2. Lora — modern open, designed for screens
3. Vollkorn — warm, weighted
4. Source Serif 4 — Adobe humanist
5. Literata — Google Books face

**Fonts dropped:**
- Bitstream Charter, Bitstream Vera Serif — installed only as Type1 .pfb, WeasyPrint silently substitutes DejaVu Serif. Documented in NOTES, removed from variant set.

**Combos worth trying** (in [VARIANTS.md](../calibre-xtc/VARIANTS.md) `## Combinations`):
- `combo-vollkorn-dropcap`, `combo-lora-dropcap`, `combo-eb-garamond-dropcap` — drop cap added to each top font
- `combo-vollkorn-ragged` — left-aligned, no hyphens
- `combo-eb-garamond-larger` — 18pt × 1.6 zoom
- `combo-source-serif-narrow` — tight 12px margins

**Files staged for SD load** (see [`samples/test_corpus/README.md`](../samples/test_corpus/README.md)): one source per Papyrix-supported format (md/txt/epub/fb2/html/xtc) + 6 full PoG XTCs (5 fonts + 1 with drop cap). 96 MB total.

**Resume here:** when SD is back, wipe + restage from `samples/test_corpus/`. Plug into device. Pick a full-PoG XTC to actually read. `XTC_FONT_FAMILY` and `XTC_DROPCAP` are the two knobs you'll most likely tune.

---

## 2026-05-02 00:55 — WeasyPrint renderer, ~20× speedup

Replaced the Qt WebEngine renderer with WeasyPrint + pypdfium2.

**Speed (Calibre Quick Start, 101-page Qt baseline equivalent):**

| Renderer       | Wall  | CPU%  | Pages | Notes                          |
|----------------|-------|-------|-------|--------------------------------|
| Qt baseline    | 57.6s | 20%   | 101   | Idle 80% of the time on delays |
| Qt fast (20/10)| 22.0s | 58%   | 101   | Just removed pessimistic sleeps|
| WeasyPrint     | 2.9s  | 101%  | 29    | Sync, fully CPU-bound          |

**Player of Games (612 KB EPUB):** 28.7s on WeasyPrint vs ~10 min on Qt baseline. ~20× faster on real content.

Pagination differs: WeasyPrint uses @page CSS pagination (semantic breaks at chapter / block boundaries), Qt path scrolls through DOM and breaks at pixel boundaries with a 1.5× zoom. Net: WeasyPrint emits ~40% as many pages; pages are denser. Visible quality is excellent — bullet lists, headings, hyperlinks all render correctly. Could match Qt density by bumping `xtc_font_size` from 16 to 24 if desired.

**Wiring:** new `weasy_renderer.py` mirrors `QtWebRenderer.render_spine_item()` but returns PIL Images. `xtc_output.py` dispatches on isinstance for PIL vs QImage. Selector: `XTC_RENDERER=weasy|qt` env var. Wrapper now defaults to weasy.

**System dep:** `weasyprint==68.1` and `pypdfium2==5.7.1` installed via `pip install --user --break-system-packages` into `~/.local/lib/python3.12/site-packages/`. Calibre's bundled Python (system /usr/bin/python3.12) picks them up automatically.

**Resume here:** if you want WeasyPrint to match Qt's denser layout, bump font size or add an explicit `xtc_zoom` equivalent. Otherwise use as-is. The whole library can be batch-converted in maybe 10 minutes total now.

---

## 2026-05-01 23:25 — Forked plugin, three real bugs fixed

User said to use `~/calibre_library` and "make our own fork." Did both.

**Fork setup:** Moved `refs/calibre-xtc/` → `calibre-xtc/` (top-level), renamed git remote `origin` → `upstream`, work happens on branch `cedar/main`. Slot for our `origin` left open if/when we push.

**Library snapshot (13 EPUBs):**
- Iain M. Banks Culture: Consider Phlebas, Player of Games, State of the Art, Use of Weapons, Excession, Inversions, Look to Windward, Matter, Surface Detail, Hydrogen Sonata
- Phil Hine — Condensed Chaos
- Tomas Bjartur — The Origami Men
- John Schember — Quick Start Guide (Calibre's tiny test EPUB, copied in)
- Plus four CJK classics (Cao Xue Qin, Luo Guan Zhong, Shi Nai An, Wu Cheng En) — these aren't in the EPUB scan but exist as authors

**Bugs fixed in fork (3 commits on top of upstream v1.0.1):**

1. `87d637d` — orphan `_register_xtc_scheme` call (NameError on every CLI run; v1.0.1 is just broken without this fix)
2. `92ad3fd` — chapter index gaps. Player of Games went from 8 chapters with 388 unmapped pages (~31% of book) to a contiguously-mapped index. Logic was: only spine items found in TOC got chapter entries. Most epubs split a chapter across multiple spine files; subsequent spine items inherit the previous chapter and same-title consecutive items merge into one entry.
3. `1bb60ba` — UTF-8 codepoint-safe truncation in `xtc_encoder.py`. Fixed-width metadata (book title 128B, author 64B, publisher 32B, language 16B, chapter title 80B) was sliced on byte indices. CJK content (3 bytes per char) gets cut mid-codepoint and the device's UTF-8 decoder sees garbage. New `_encode_utf8_fixed()` walks back to a complete-codepoint boundary.

**Validation:**
- eng.epub re-converted: 12 → 13 chapters, page ranges contiguous, validator clean.
- Player of Games re-converting in background (long; ~10 min in software RHI).
- Real Banks EPUB cover renders cleanly — the earlier "Cover image extracted: 1x1" was Calibre's eng.epub legitimately shipping a 1×1 placeholder.

**Tooling added:**
- `bin/epub2xtc` — wrapper that sets the Qt env vars
- `bin/convert-library` — sequential batch convert of every EPUB in `~/calibre_library` to `samples/library/*.xtc`, skips already-done files
- `calibre-xtc/NOTES.cedar.md` — fork-change summary suitable for upstream PRs

**Resume here:** wait for PoG re-conversion → verify chapters now contiguous, eyeball pages → run `bin/convert-library` to stress-test the whole library (especially CJK) → file 3 PRs upstream → sideload XTC to X4 once SD card returns.

---

## 2026-05-01 22:57 — Working EPUB→XTC pipeline on Linux

**Big pivot:** `thirteen37/calibre-xtc` already does what we'd build. Stopped the format-spec bg agent (work would have duplicated `refs/calibre-xtc/CLAUDE.md`, which has a complete spec). Reverse-engineering Papyrix XTC code is no longer required.

**What works now:**
- Calibre 7.6.0 (system) + plugin v1.0.1 cloned to `refs/calibre-xtc/` and installed via `calibre-customize -b`
- Wrapper at `bin/epub2xtc` runs `ebook-convert` with the env vars that make Qt WebEngine actually render
- Sample: `samples/eng.xtc` — Calibre's 79-page Quick Start Guide → 101-page XTC, 4.6 MB, validator says `✓ VALID`
- Extracted PNGs in `samples/extracted/` look clean: real text, sharp Floyd-Steinberg dithering, progress bar with chapter ticks

**Two upstream bugs I worked around:**

1. **`_register_xtc_scheme` NameError in `renderer.py:113`** — orphan reference left over from commit `44e9489 Revert font embedding support`. The function definition was deleted but a call site survived. Patched locally by deleting the call. **Worth a one-line PR upstream.**
2. **Qt6 RHI backend init fails** in CLI / headless / no-GPU contexts — silently produced near-blank pages with only the progress bar overlay. Symptom: `Failed to create QRhi for QBackingStoreRhiSupport` / `No suitable graphics backend found`. Fix is env-only:
   ```
   QT_QUICK_BACKEND=software
   QT_RHI_BACKEND=null
   LIBGL_ALWAYS_SOFTWARE=1
   ```
   Plugin should probably set this internally when no display/GPU available, or warn. **Worth a heads-up issue upstream.**

**Format spec lives in `refs/calibre-xtc/CLAUDE.md`** — header, page index, XTG header (22 bytes!), chapter index (96 bytes, 1-based pages, little-endian), bitmap encoding. No reverse engineering needed.

**Validation tooling I'll keep using:**
- `python3 refs/calibre-xtc/tests/verify_xtc.py <file.xtc> --verbose` — comprehensive structural validator
- `calibre-debug -e refs/calibre-xtc/tests/extract_xtc.py -- <file.xtc> -p 1-10 -o out/` — page extraction to PNG for eyeballing

**Not yet done:**
- Sideload `eng.xtc` to the X4 SD card and confirm Papyrix actually opens it without `Indexing...` (SD card is currently pulled — another agent is working on book inventory)
- File the two upstream bug reports
- Decide whether the project still needs a Rust converter (probably no — the calibre plugin works; main motivation gone)

**Resume here when SD card is back:** copy a known-good XTC to SD, plug back in, open in Papyrix, watch for indexing overlay.

---

## 2026-05-01 22:50 — Project bootstrap

**Context carried in from the device-flashing session:**

- Device: Xteink X4 e-paper reader, ESP32-C3 (rev v0.4), 16 MB flash, 480×800 1-bit display
- MAC: `88:56:a6:f1:bf:a0`
- Just flashed Papyrix v1.20.3 firmware (`bigbag/papyrix-reader`)
- Boot bug found: first boot without SD card spams `[ERR] [STATE] No state registered for id 9` (state 9 = `Sleep`); fixed by reflashing with SD inserted. Worth filing upstream.
- Reading bug found: every page turn shows `Indexing...` overlay. Source of truth in `src/states/ReaderState.cpp` `renderCachedPage()`:
  - `stopBackgroundCaching()` is called at the start of every render
  - foreground synchronously calls `createOrExtendCache()` if current page not in cache
  - background pre-cache resumes only if `!pageCache_ || !thumbnailDone_` — so once initial cache exists, it never refills ahead
  - net effect: every time you outrun the cached extent within a chapter, you stall
- XTC files (Xteink's native format) bypass this entirely: flat page index, pre-paginated. Comment in `ReaderState.cpp`: "Skip for XTC — uses flat page indexing, no cover page concept in reader"
- Papyrix supports XTC as a format; ContentType enum: `Epub, Xtc, Txt, Markdown, Fb2, Html`
- "XTCH" appears as a compressed cousin (per CHANGELOG)

**Project goal:** Build Rust EPUB→XTC converter, eliminate indexing pauses.

**Tooling staged:**
- esptool installed via pipx
- papyrix-flasher v0.4.0, firmware.bin v1.20.3 at `~/Downloads/papyrix/`
- Serial access via `sg dialout -c '...'` (user added to dialout this session; new shells inherit; this Claude process does not until restarted)

**Spawned (background):**
- Agent A — Extract XTC format spec from Papyrix source code
- Agent B — Search community for existing reverse-engineering / converters / SUMI fork
- Agent C — Locate Xteink official PC client download + Wine compatibility

**Next on resume:** Read agent reports, consolidate format spec under `re/XTC_FORMAT.md`, then either set up Wine to verify or start Rust skeleton if spec is solid enough.
