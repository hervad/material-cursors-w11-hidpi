# Credits

**Artwork:** Material Cursors by Alexey Varfolomeev (varlesh) - <https://github.com/varlesh/material-cursors>
(gnome-look: <https://www.gnome-look.org/p/1346778>). License: GPL-2.0. The repository's `LICENSE` is the GPL v2 text
with no "version 2 only" or "or later" notice anywhere; [LICENSE](LICENSE) here is that file, byte-for-byte
(SHA-256 `189b1af95d661151e054cea10c91b3d754e4de4d3fecfb074c1fb29476f7167b`).
The upstream `AUTHORS` file also credits its build script to Sergei Eremenko and Keefer Rourke (from
capitaine-cursors, modified by Efus10n & jnzhng). That script is not used here.
**Source:** `upstream/` is a git submodule pinned to commit `2a5f302fefe04678c421473bed636b4d87774b4a` (2022-08-17,
the latest; the gnome-look release `20220818` matches it).
**Windows 11 HiDPI port:** Vadym Herman ([@hervad](https://github.com/hervad)), built with
[w11-cursor-toolkit](https://github.com/hervad/w11-cursor-toolkit)

## Changes from upstream

- Re-rendered from the original 32 px SVGs (`src/material_*_cursors/*.svg`) at every Windows cursor size, no
  resampling. The hand-tuned `*_24.svg` twins are not used.
- Drop shadow left out: upstream draws it as blurred black copies of each shape (every element with an SVG filter;
  386 per variant, all 30 % black). Windows draws its own pointer shadow (toolkit ADR-14).
- Hotspots: tips as in upstream's `src/config/*.cursor` (32 px line). Centred cursors (busy, precision, unavailable,
  resize, move) use (16, 16) and text (15.5, 15.5), the measured centre of the drawing; upstream's (15, 15) is a
  pixel index that drifts off-centre at large sizes.
- Animation: 24 frames as upstream; 30 ms per frame approximated in whole 1/60 s steps (2,2,1,2,2,... = 717 ms per
  turn instead of 720 ms).
- Windows role mapping incl. Pin and Person (both use the pointing hand). No files in overrides/.
