Valence vs. Other QWK Systems
=============================

With the advent of Searchlight Software's new QWK/SL, there's been some
discussion of its pros and cons vs. Valence. I decided to make a feature
comparison chart, and include QWKRick and the now-defunct SLQWiK. This
list is by no means complete:

|                            | Valence | QWK/SL  | QWKRick | SLQWiK  |
| -------------------------- | ------- | ------- | ------- | ------- |
| **Searchlight features:**  |         |         |         |         |
|                            |         |         |         |         |
| Netmail                    |  Yes    |  Yes    |  No     |  No     |
| Internet email             |  Yes    |  Yes    |  No     |  No     |
| Long subjects (>25 chars.) |  Yes    |  No     |  No     |  No     |
| Included files             |  Yes    |  No     |  No     |  No     |
| Translate color codes      |  Yes    |  No     |  No     |  No     |
| Metacharacters             |  Yes    |  No     |  No     |  No     |
| Underline joining          |  Yes    |  No     |  No     |  No     |
| Post with alias            |  Yes    |  Yes    |  No     |  No     |
| Language strings           |  No     |  Yes    |  No     |  Yes    |
| Edit subboard list         |  Yes    |  Yes    |  No     |  No     |
| Over ~1000 subboards       |  Yes[^1]|  Yes    |  No     |  No     |
| Max. lines per message     |  650    |  1000   |  400    |  400    |
| Version compatibility      |  2.15+  |  4.5+   |  3.0+   |  2.15+  |
|                            |         |         |         |         |
| **QWK features:**          |         |         |         |         |
|                            |         |         |         |         |
| Control messages           |  Yes    |  No     |  Yes    |  Yes    |
| DOOR.ID                    |  Yes    |  No     |  Yes    |  Yes    |
| Ordered CONTROL.DAT        |  Yes    |  No     |  No     |  No     |
| Pointer files              |  Yes    |  No     |  No     |  No     |
| Bulletins                  |  Yes    |  No     |  No     |  No     |
| Welcome/Goodbye screens    |  Yes    |  Yes    |  No     |  No     |
| SESSION.TXT                |  Yes    |  No     |  No     |  No     |
| Fido tearline fixing       |  Yes    |  No     |  Yes    |  Yes    |
| Translate high-bit chars   |  Yes    |  No     |  No     |  No     |
| Archiver choice            |  Yes    |  No     |  No     |  No     |
| Use any protocol           |  Yes    |  No     |  No     |  No     |
| User control of packet size|  Yes    |  No     |  No     |  No     |
| Personal/to All scanning   |  Yes    |  No     |  No     |  No     |
| Multiple packets in one D/L|  Yes    |  No     |  No     |  No     |
| Text packets               |  Yes    |  No     |  No     |  Yes    |
|                            |         |         |         |         |
| **RIP features:**          |         |         |         |         |
|                            |         |         |         |         |
| Mouse support in --        |         |         |         |         |
|  Subboard list             |  Yes    |  Yes    |  N/A    |  N/A    |
|  Prompts                   |  Yes    |  Yes    |  No     |  No     |
|  Options                   |  No     |  Yes    |  Yes    |  No     |
|                            |         |         |         |         |
| High-resolution display    |  No     |  Yes    |  No     |  No     |
|                            |         |         |         |         |
| **Time:**                  |         |         |         |         |
|                            |         |         |         |         |
| Secs to scan ~3700 messages|  37     |  85     |  75     |  N/A    |
| Secs to scan and pack      |  90     |  165    |  135    |  N/A    |


In the time test, SLQWiK was excluded because it couldn't get through
the scan without locking up. Scanning is the only part of the process
that's really under the control of the QWK program; the packing in each
case was done by PKZIP, at around a minute for each packet. The
resulting packets were about 900k each. The comparison is approximate
because Valence found 3716 messages, while QWK/SL only got 3679. (This
doesn't necessarily mean QWK/SL was skipping messages, though it may.)
All programs were set to a maximum of 500 messages per subboard, 5000
per packet. The test system was a 486dx33 running DOS 5.0 and Hyperdisk.

Versions compared: Valence 1.4x, QWK/SL 1.1, QWKRick 1.01, SLQWiK 1.02d.
(The QWK/SL version number was found only in the FILE_ID.DIZ; I
understand there's a newer version which is supposed to be faster.)

[^1]: As of Valence 1.7.

---
*[Home](https://wmcbrine.com/) / [Searchlight](https://wmcbrine.com/sl/)*
