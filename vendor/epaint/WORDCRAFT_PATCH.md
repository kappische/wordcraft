# WordCraft patch: whole-pixel glyph advances

Upstream: epaint 0.36.2 (MIT OR Apache-2.0), copied unchanged from the crates.io
release (Git 49682f8baa058bf49e011035cfbd6e825f88a5ef, `crates/epaint`), with the
registry's normalized `Cargo.toml`, `Cargo.toml.orig` and `README.md`. The upstream
`Cargo.lock` is omitted. `LICENSE-MIT` and `LICENSE-APACHE` are egui's shared
upstream licences (the registry package does not ship them). Wired in by
`[patch.crates-io]` in the workspace `Cargo.toml`; not a workspace member.
`rustfmt.toml` keeps upstream's default formatting under `cargo fmt --all`.

## Change

`src/text/text_layout.rs`, in the shaping loop: below 2 pixels per point, each
glyph's advance is rounded to whole physical pixels (after the tab and thin-space
overrides, before letter spacing).

Why: egui keeps the shaper's fractional advances, so at 1x–1.5x the gaps between
letters vary by up to a pixel and stems straddle pixel boundaries, which reads as
soft, uneven UI text next to native Windows text. Rounding the advances keeps
every glyph on the pixel grid (WordCraft also turns off `subpixel_binning`).
At 2x and above the exact advances are kept. Text widths change by at most half a
pixel per glyph. There is no egui option for this.

Upstream SHA-256 of the patched file: `src/text/text_layout.rs`
`4bce784f039399fec7a72ad5838573a6746db2ac2847851acbc83faf635b08ca`.

## Upgrading egui

Copy the new epaint release over this directory, re-apply the block marked
`WordCraft patch` in `text_layout.rs`, update the versions and hash above, and
check UI text at 1x (`ui_shot` with `with_pixels_per_point(1.0)`).
