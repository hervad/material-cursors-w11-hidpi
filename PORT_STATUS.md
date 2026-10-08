# Port status: Material Cursors

- gnome-look page: https://www.gnome-look.org/p/1346778 (store API, 2026-10-08: version `20220818`, three downloads
  `material-cursors`, `material-dark-cursors`, `material-light-cursors`)
- upstream: https://github.com/varlesh/material-cursors, submodule `upstream/` pinned to
  `2a5f302fefe04678c421473bed636b4d87774b4a` ("update #4", 2022-08-17). Checked 2026-10-08: still the latest commit
  (an earlier note here said "Nov 2023"; GitHub dates it 2022-08-17).
- upstream license: GPL-2.0. `LICENSE` = GPL v2 text (18,431 B, SHA-256 `189b1af9…f7167b`), copied byte-for-byte
  to ./LICENSE. No file states "only" or "or later" (only `build.sh`, `Makefile`, `LICENSE` mention a licence or
  copyright at all); GPL-2.0-only is correct under either reading. `AUTHORS`: cursors by Alexey Varfolomeev
  (varlesh); the build script comes from capitaine-cursors (LGPL-3.0) - not used here, so its licence doesn't
  reach the artwork. nixpkgs labels the package gpl3Only; the file says v2.
- variants in scope: default, dark, light (exactly the three on the page)

## Status
Public at https://github.com/hervad/material-cursors-w11-hidpi (2026-10-08), built by CI with toolkit **v0.2.0**
(first tag with `[render] strip_filtered`). CI files match the local build image-for-image (bytes differ: encoder).

## Findings
- **Canvas:** every main SVG is `width="32" height="32"` with no viewBox -> `design_canvas = 32`. Each cursor also
  has a `*_24.svg` twin (`width="24"`); upstream's build renders 24/48 px from the 24 art and 32/64 px from the 32
  art. Here everything is rendered from the 32 art. (Possible later improvement: use the 24 art for multiples of 24.)
- **Shadow:** baked in as SVG filters. All 600 SVGs checked: 1,158 filtered elements (386 per variant), every one a
  top-level black fill at opacity 0.3 without stroke; nothing else uses a filter. `strip_filtered = true` removes
  exactly those (ADR-14). With no filters left, cairosvg renders the art correctly.
- **Hotspots** (`src/config/*.cursor`, Xcursor pixel indices; upstream's 64 px values are exactly 2x the 32 px ones):
  tips kept as upstream (arrow/help/working 1,1; pencil 2,29; up-arrow 15,2; hand 13,5). Centred cursors measured
  from a 512 px render with the shadow stripped: crosshair, busy, unavailable, size_*, fleur centred at
  (16.00, 16.00); text at (15.50, 15.50). Upstream's (15, 15) would sit 8 px off-centre at 256 px.
- **Animation:** progress/wait, 24 frames, 30 ms each (all 96 config lines) = 1.8 jiffies -> spread
  `2,2,1,2,2,...` = 43 jiffies = 716.7 ms per turn (720 ms upstream).
- **.ani size:** working.ani ~1.02 MB (24 frames x 9 sizes) > the toolkit's 1 MB download cap; the cap is a
  download-size policy, not a loader limit (largest image offset 35,348 of 65,535). Maintainer's decision
  (2026-10-08): `ani_budget_bytes = 1_100_000` for Material only.
- **Upstream `windows/` folder** (inspected read-only, nothing copied): 15 files, each one 32 px BMP image;
  animations at 3 jiffies (50 ms) per frame = 1.2 s per turn; no Pin/Person.
- **Sharpness** (2026-10-08): no layer is resampled; anti-aliasing is one pixel deep at every size (partial pixels
  not on the outer edge: 0-3.4 %, Microsoft aero 0-5 %). The 24-art is NOT crisper at 48/72 px (rim 32.9 vs 29.4 %).
  Perceived softness of Default/Dark on dark windows is contrast, not blur: the outline is black at 75 % opacity
  (WCAG vs #202020: outline 1.2:1, body Dark 2.0:1 / Default 3.0:1 / Light 12.8:1). Maintainer's decision: keep the
  upstream look (option A); README says which variant suits which background.
- **Load cost** (Windows 11 25H2, same run as Microsoft aero): static 0.4-0.6 ms (aero 0.3-0.8), animated 5-11 ms at
  32-96 px (aero 3-13), 87 ms at 256 px (aero 59); 0 GDI/USER handles leaked over 300 loads per file.

- **v0.1.1 rename** (maintainer, 2026-10-08): scheme "Material W11 HiDPI" -> "Material Blue Grey W11 HiDPI" so every variant is named in
  Mouse Properties; variant id default -> blue-grey (zip material-blue-grey-*). Same cursors (README previews regenerate byte-identical). README: upgrade note.

## Checklist
- [x] Upstream pinned (submodule at a commit); latest commit confirmed
- [x] License verified by reading the actual LICENSE file -> ./LICENSE (byte-identical)
- [x] design_canvas confirmed from SVG width/height (32)
- [x] All 17 roles mapped; diagonals checked in the preview (dgn1 = size_fdiag, dgn2 = size_bdiag); Pin/Person = link
- [x] Hotspots from upstream config; centred ones measured
- [x] `w11cursor build` + `validate` green locally; Test-LoadCursors 51/51; Get-AniFrameTiming 6/6
- [x] Upstream Windows set inspected with `w11cursor inspect` -> README
- [x] README (Capitaine layout), CREDITS, preview image
- [x] Installed on Windows 11 25H2 (2026-10-08): 3 variants, loader 51/51 on C:\Windows\Cursors, live cursor = files;
      maintainer's go-ahead for v0.1.0 after the sharpness/contrast review (option A)
- [x] Toolkit v0.2.0 tagged; GitHub repo created (public); CI green
- [x] tag v0.1.0 (2026-10-08)
