# DMG Demo

A Game Boy demo written in the Op language. The screen shows six lines
of text. Each line shows the capitalized simple name of the font that
renders it. The demo cycles the four DMG monochrome background palette
shades in lockstep.

## About

The demo targets the Game Boy (sm83-nintendo-gameboy). It uses the
`std` library for CPU, machine, and font definitions.

The demo does the following:

1. Disables the LCD.
2. Loads the glyph subsets for six font names into the VRAM pattern
   table at 0x8000, one font at a time (27 tiles in total).
3. Clears the tile map at 0x9800 and writes six name strings at
   column 2, rows 2 through 7.
4. Enables the LCD with the background display bit set.
5. Enters a loop that waits for vblank, then steps the background
   palette through 0x40, 0x81, 0xC2, 0x03 — one step every 6 vblanks,
   then repeats.

## The Six Lines

| Row | Text | Font constant |
|---|---|---|
| 2 | Nes | `FONT_NES_SMALL_NORMAL` |
| 3 | Ibm | `FONT_IBM_SMALL_NORMAL` |
| 4 | Ibm Fixed | `FONT_IBM_FIXED_SMALL_NORMAL` |
| 5 | Italic | `FONT_ITALIC_SMALL_NORMAL` |
| 6 | Spect | `FONT_SPECT_SMALL_NORMAL` |
| 7 | Min | `FONT_MIN_SMALL_NORMAL` |

## How the Demo Works

### Glyph Subset Loading

The `font_load_string` function copies one VRAM tile slot per
character in the given string. The glyphs land in fresh slots starting
at `font_first_free_tile`, and `_tile_remap[char]` records the slot.
Only 27 tiles are loaded — the six fonts together would need 838
tiles, far more than the 256 tiles of the DMG pattern table.

The per-character work runs in the single-copy loop functions
`_fls_loop` and `_dt_loop` (in `std/src/font/gameboy.op`). Their
labels stay unique because each body is placed once in ROM.

### Text Display

The `draw_text` function looks up each character in `_tile_remap` and
writes the absolute tile slot to the tile map at 0x9800 + row * 32 +
column. Characters whose remap entry is unset fall back to the legacy
`char + first_tile` index.

### Palette Animation

The main loop polls LY for vblank, counts frames in `_frame_cnt`, and
every 6 vblanks steps `_pal_step` through 0, 1, 2, 3 and writes the
matching BGP value to `LCD::BG_PALETTE` (0xFF47): 0x40, 0x81, 0xC2,
0x03. All four colour indices step together, so the ink stays one
shade darker than the paper; the last step inverts.

### Vblank Interrupt

The vblank interrupt handler at vector 0x0040 contains a `RETI`
instruction. The main loop polls the vblank status rather than relying
on the interrupt.

## Building

```
cart build
```

This produces `target/sm83-nintendo-gameboy/dmg-demo.gb`.

## Running

```
cart run
```

This launches the `sameboy` emulator with the built ROM.

## Requirements

- `opc` compiler (0.11.0 or later — needs the non-inline fn import and
  the SM83 register-to-register loads)
- `cart` build tool
- `sameboy` emulator on PATH

## License

Apache-2.0