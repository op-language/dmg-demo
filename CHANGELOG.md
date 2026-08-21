# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0]

### Added
- Game Boy demo displaying "Op Code!" and cycling the four DMG
  monochrome background palette shades.
- `src/cart.op` with vblank interrupt handler, font loading, text
  display, vblank wait, and palette animation functions.
- `src/font.2bpp` with 8 Game Boy 2bpp font tiles (128 bytes) for the
  characters in "Op Code!".
- `Cart.toml` with sm83-nintendo-gameboy target, gb format, std 0.5.0
  dependency, and sameboy run profile.
- `README.md` documentation.