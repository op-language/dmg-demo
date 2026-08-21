# DMG Demo

A Game Boy demo written in the Op language. This demo is a port of the
nes-demo to the Game Boy target. It displays the text "Op Code!" and
cycles the four DMG monochrome background palette shades.

## About

The demo targets the Game Boy (sm83-nintendo-gameboy). It uses the
`std` library for CPU and machine definitions.

The demo does the following:

1. Disables the LCD.
2. Loads 8 font tiles (128 bytes of 2bpp data) into the VRAM pattern
   table at 0x8000.
3. Writes 8 tile indices to the tile map at 0x9800 to display "Op
   Code!".
4. Enables the LCD with the background display bit set.
5. Enters a loop that waits for vblank, then cycles the background
   palette through the four monochrome shades (white, light gray, dark
   gray, black).

## How the Game Works

### Font Loading

The `load_font` function sets HL to the VRAM pattern table address
(0x8000) and writes 128 bytes of 2bpp tile data using `LD A, #value`
and `LDI` (LD (HL+), A) instructions. Each Game Boy tile is 16 bytes
in 2bpp format (2 bits per pixel, 2 planes).

### Text Display

The `write_text` function sets HL to the tile map address (0x9800 +
0x20 for row 2) and writes tile indices for each character in "Op
Code!". The tile indices are: O=1, p=2, space=0, C=3, o=4, d=5, e=6,
!=7.

### Palette Animation

The `pal_animate` function increments a counter, masks it to 2 bits
(0-3), and writes it to the `LCD::BG_PALETTE` register (0xFF47). The
four shade values are: 0=white, 1=light gray, 2=dark gray, 3=black.
The background color cycles through these four shades on each vblank.

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

- `opc` compiler
- `cart` build tool
- `sameboy` emulator on PATH

## License

Apache-2.0