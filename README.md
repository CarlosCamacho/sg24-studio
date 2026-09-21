About SG24 mode

Semigraphics 24 gives the MC6847 a 64×192 grid of cells, each 4 screen pixels wide by one scanline tall. That is the 4:1 shape this editor draws by default.

1 N2 N1 N0 P0 P1 X X
↑      ↑      ↑  ↑  ↑
MSB    chroma  L  R  unused
One byte carries two horizontally adjacent cells and a single three-bit chroma that both share. Two neighbours can be different colours only if a byte boundary falls between them — which is why columns pair 0+1, 2+3, and why this editor resolves the partner cell every time you paint. Sixty-four cells across is thirty-two bytes across, which is what the byte grid marks.

The buffer is 6144 bytes. SG24 is not a native 6847 mode: it appears when the SAM streams a larger buffer into the VDG’s SG4 pins, so it needs a SAM alongside the VDG.

Bit 7 is load-bearing. It is what marks a byte as a graphics group at all. With it clear the VDG reads the very same byte as a text character, so an empty cell pair on a screen is $80 — never $00. A sprite record can still use $00 as an empty marker, but only if the blitter skips those bytes rather than storing them.

Twelve colours, not nine
That same bit is how you get past eight chromas. An alphanumeric cell has its own foreground and background, and a blank glyph paints a solid field of one of them: $20 is a space, $60 the same space inverted. BASIC clears the text screen to $60, which is why a CoCo boots bright green.

Which pair you get is $FF22 bit 3, the colour set. It does not touch the eight semigraphics chromas, so the choice costs nothing:

Set 0 — bright green on dark green. Bright green repeats chroma green, so this adds one colour: ten on a screen.
Set 1 — on a CoCo 1 or 2 this is a dark red on a light pink, neither of which matches a chroma, so it adds two: eleven on a screen. A CoCo 3 renders the same pair as dark orange on orange.
Eight chromas, black, dark green and the set-1 pair is the twelve-colour hardware palette. All twelve at once needs bit 3 flipped mid-frame, which is a raster-timing exercise; ten or eleven need nothing but the right byte.

A text byte is not limited to blanks. Because one SG24 byte is one scanline, the VDG shows glyph row y mod 12 — so a non-blank character gives an 8-pixel, 1-bit pattern from the font ROM at single-pixel horizontal resolution, four times finer than a semigraphics cell, in the two colour-set shades. This editor only uses the blank glyphs, whose rows are all identical and therefore safe at any y.

Machine support. SG24 works on the CoCo 1 and 2 and the Dragon. On a CoCo 3 the GIME only handles SG4, so SG24 artwork will not display correctly there without a GIME-specific workaround.
