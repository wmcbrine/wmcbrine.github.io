rletopbm / zxtopbm
==================

A couple of image format converters that I wrote for [NetPBM], that I
always meant to publish, but never quite got around to. These are written
in standard ANSI C89.


rletopbm
--------

CompuServe RLE was a very simple, 1-bit monochrome, 256 by 192 pixel image
format, supposedly based on PMODE 4 of the TRS-80 Color Computer (as it
happens, the same mode used by [Saucer]).

![Kate]

Sample RLE pic, converted and scaled up. The original: [kate.rle] (I
created this image by "hand digitizing" the cover of Kate Bush's "The
Whole Story", using graph paper, artistic license, and a lot of pixel
twiddling.)

I first learned about RLE from an [article in Time Designs] magazine,
which included a BASIC program to render them to the screen on a Timex
Sinclair 2068. From there, I wrote my own converters, as part of Draw 512,
and later this one. I never did get a proper specification for the format
(still haven't), just worked off the article and some sample files I got
from BBSes.

{% gist e753ce1da229c655f17f69ac57da93f9 %}

Nowadays, NetPBM comes with RLE support included, so you probably don't
need this. But it's still interesting to see the different approaches.
Since the "RLE" identifier was already being used for Utah RLE, they chose
"CIS" (Compuserve Information Service), i.e. cistopbm and pbmtocis.

I'd already converted all my RLEs, so I haven't really used cistopbm,
except to verify that it produced the same output as rletopbm on kate.rle.
And then I used pbmtocis to regenerate kate.rle, because cistopbm didn't
quite like my 2068-generated version. :)


zxtopbm
-------

The main display mode of the Timex Sinclair 2068 -- based on that of the
original ZX Spectrum -- has the same 256 x 192 pixel resolution, but with
colors that work more like the text mode on a PC, with a foreground and
background color for each 8 x 8 cell. But me... I had a monochrome
monitor. So I wrote a converter that only handled the pixels, and not the
colors, with the idea that I'd get around to that.

![2068]

Self-portrait of a Timex Sinclair 2068, which I'll describe further later.

Since there are vastly more Spectrums than 2068s in the world -- and the
picture format is the same -- I've labelled this converter as being for
Spectrum images.

{% gist dec624a45e5da319f2c32138195362d0 %}

Little did I realize that zxtoppm was already a thing, by Russell Marks --
find it in the "zspeccyutils" package at [ibiblio]. He also did a reverse
version (ppmtozx).

---
*[Home]*

[NetPBM]: http://netpbm.sourceforge.net/
[Saucer]: https://wmcbrine.com/saucer/
[Kate]: kate.webp
[kate.rle]: kate.rle
[article in Time Designs]: https://www.timexsinclair.com/article/run-length-encoded-graphics/
[2068]: self2068.webp
[ibiblio]: https://www.ibiblio.org/pub/Linux/system/emulators/zx/!INDEX.html
[Home]: https://wmcbrine.com/
