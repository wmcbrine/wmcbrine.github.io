Y2K issues in Searchlight BBS
=============================

The Searchlight internal date format uses one unsigned binary byte to
represent the year, as well as one byte each for the month and day. In
versions prior to 5.0, the year was stored as (year mod 100); that is
(in decimal terms), as the last two digits. 1999 is stored as 99, and
2000 will be stored (and displayed) as 00.

In SL 5.0, the displayed year was changed to use four digits, and the
internal date was redefined as (year - 1900). Thus, the year 2000 will
be stored as 100. (For years between 1900 and 2000, the definitions are
equivalent.) This redefinition has value, because it allows the year
value to be unambiguous; but it also breaks some things, like the old
version of the `strdate()` function from the Searchlight Programmers'
Library, which will now show "100" for the year 2000. Furthermore, it
limits the maximum year value to 1900 + 255, or 2155; but before that,
MSDOS itself will run out of time, at the end of 2099.

The routine which displays the day of the week in Searchlight works by
comparing the date with a known day, via the `datediff()` function, and
using that value mod 7 as the index to a string array. The date it uses
is 1-1-87. Unfortunately, the `datediff()` routine only returns a 16-bit
signed integer, which means that it breaks after 32767 days. Hence, in
Searchlight 5.x, the day of the week shows up incorrectly after
9-17-2076. (It prints things like "December" where it should say "Wed".)
In earlier versions, it breaks much sooner -- on 1-1-2000. Granted, this
is purely a cosmetic problem.

Most Y2K problems with SL 4.5 will be cosmetic; e.g., the day of the week
will be printed incorrectly.

In previous versions of this text, I followed that with "However, things
will work more smoothly all around if you upgrade to SL 5.x before the
year 2000." But I must now retract that. It turns out that SLMAIL hasn't
been informed of the format change; it refuses to export messages with a
2000 year, and imports them as "1900". To work around these problems,
I've released "Makedate", available in the package
[mkdat12c.zip](mkdat12c.zip) (q.v.).


Y2K issues in Valence
---------------------

Beginning with version 1.6, I've taken measures to ensure that Valence
is Y2K-ready. Since the Searchlight Programmer's Library has not been
updated since 4.5, I've written my own replacements for `strdate()`, and
also for `datediff()`. (I could've managed -- as SL 5.x does -- with the
old `datediff()`; but only until 2076. This way, Valence gets 22 more
years to live.) I've also adopted the (year - 1900) system for all
internal dates.

When working with SL 4.5 or earlier, Valence 1.6+ will add or subtract
100, as appropriate, when dealing with dates from the Searchlight message
base, in order to be consistent with what those versions of Searchlight do
internally. A fixed window is used here: year values less than 80 (the
first MS-DOS year) are considered to be 20xx, while 80-99 are 19xx.

Then there's the QWK format, which uses (year mod 100) in its dates. When
posting messages to SL 5.0+, Valence 1.6+ will add 100 to the year found
in the QWK header, if that year is less than (current year - 1950). This
gives 50 years on either side of the current year. Example: The current
year is 1999, so the dividing point is 49. Years 00 through 49 are taken
to represent 2000 through 2049, while 50 through 99 mean 1950 through 1999.
This system will work through 2099.

---
*[Home] / [Searchlight]*

[Home]: https://wmcbrine.com/
[Searchlight]: https://wmcbrine.com/sl/
