The Mysterious QWK-File Format
==============================

by Jeffery Foy

It would be safe to assume that if you're reading this article, you use
or have used a QWK-compatible offline mail reader. The QWK format has
emerged as the format of choice due to the relatively small size of QWK
mail packets as compared to an equivalent ASCII text file.

As most users of offline mail readers know, the QWK format was designed
by Mark Herring (Sparky) of Sparkware. While Mr. Herring did design the
format, he only gave very sketchy details as to the specifics of the
format. This is quite understandable as he is a very busy person. That
is the reason why I'm writing this article.

In its most basic form, a QWK file is simply a compressed file. In
almost all cases, the QWK file has been compressed with PKZIP from
PKWARE. With most mail doors, you can usually choose your favorite
archiver so your QWK file may not be in PKZIP format.

Within the compressed QWK file are quite a number of other component
files. We'll start with the one called **CONTROL.DAT** since it is the
easiest to describe. It is an ASCII text file so if you have one handy,
you can follow along.


```
Generic BBS            ; Line # 1
Seattle, WA            ; Line # 2
206-555-1212           ; Line # 3
Joe Sysop, Sysop       ; Line # 4
00000,GENBBS           ; Line # 5
01-01-1991,00:00:00    ; Line # 6
MARY USER              ; Line # 7
MENU                   ; Line # 8
0                      ; Line # 9
0                      ; Line #10
254                    ; Line #11
0                      ; Line #12
Main Conf              ; Line #13
...                    ; Line # x
254                    ; Line # x
Last Conf              ; Line # x
HELLO
NEWS
GOODBYE
```

1. This is the BBS name where you got your mail packet.
2. This is the city and state where the BBS is located.
3. This is the BBS phone number.
4. This is the sysop's name.
5. This line contains first the serial number of the mail door
   followed by the BBS ID. Note the BBS ID as it will be used
   later in this article.
6. This is the time and date of the packet.
7. This is the uppercase name of the user for which
   this packet was prepared.
8. This line contains the name of the menu file for those who
   use the Qmail reader/door. Almost all other mail doors leave
   this line blank.
9. No one seems to know what this line is meant for.
10. No one seems to know what this line is meant for.
    (Note: Both of these ALWAYS seem to be 0)
11. This line is the maximum number of conferences MINUS 1.
12. This line is the first conference's number. It is usually 0
    but not always.
13. This line is the name of the first conference. It is 10
    characters or less.

Lines 12 and 13 are repeated for as many conferences as listed in line
11.

Anything you see after the last conference name can be ignored as that
information isn't usually provided by mail doors. One exception to this
is the Markmail door.

Now we'll talk about the message file itself. If you haven't guessed by
now, it is the **MESSAGES.DAT** file. This is, quite obviously, the
largest file in the .QWK packet.

MESSAGES.DAT is organized very specifically into 128-byte records. The
first record is the Sparkware copyright notice. The rest of the record
after the copyright notice is filled with blanks (spaces). To maintain
compatibility with Sparky's Qmail Door, all mail doors reproduce the
copyright notice exactly.

Following the first record begins the "meat" of the message file. Each
message included in the file consists of a header followed directly by
the message text itself. First we will describe the header:


```
 Header    Field
Position   Length   Description
--------   ------   ----------------------------------------
1          1        Message status byte
                       ' ' = public message which hasn't been
                             read
                       '-' = public and already read
                       '*' = private message
                       '~' = comment to sysop which hasn't
                             been read by the sysop
                       '`' = comment to sysop which HAS been
                             read by the sysop
                       '%' = password protected message that
                             hasn't been read (protected by
                             sender of message)
                       '^' = password protected message that
                             HAS been read (protected by
                             sender of message)
                       '!' = password protected message that
                             hasn't been read (protected by
                             group password)
                       '#' = password protected message that
                             HAS been read (protected by
                             group password)
                       '$' = password protected message that
                             is addressed to ALL (protected
                             by group password)
2         7         Message number coded in ASCII
9         8         Date coded in ASCII (MM-DD-YY)
17        5         Time coded in ASCII (HH:MM) 24 hour
                    format
22        25        Uppercase name of person message is TO
47        25        Uppercase name of person message is FROM
72        25        Subject of message
97        12        Message password. Usually not anything
                    but spaces (to denote no password)
109       8         Message # this message refers to (coded
                    in ASCII)
117       6         Number of 128-byte chunks in the actual
                    message (includes header and is coded in
                    ASCII)
123       1         Determines whethere a message is live
                    (active) or killed. 90% of the time you
                    won't see a killed message in a packet.
                        'a' = Message is active/alive (0xE1)
                        'b' = Message is killed/dead  (0xE2)
124       1         Least significant byte of conference
                    number.
125       1         Most significant byte of conference
                    number. NOTE: This isn't in the original
                    .QWK format but has become the standard
                    due to conference numbers greater than
                    255. In the original format, this byte
                    was space-filled.
126       3         Filler bytes for future expansion.
                    Space-filled and usually ignored.
```

Following the header record comes the message text itself. The message
text is simply the body of the message. To save space, the return /
linefeed combination is translated to the pi character 'c' (0xE3). Note
that the last line of the message is padded with spaces to fill out the
128-byte record.

Now we'll talk about the **\*.NDX** files that are included in the
packet. Each .NDX file is formatted into records of 5-bytes each. The
bytes in each record are formatted thusly:


```
Start  Field
Byte   Length   Description
----   ------   --------------------------------------------
1      4        This is a floating point number in the MSBIN
                format. This number is the record number of
                the message header in MESSAGES.DAT that
                corresponds to this message.
5      1        This byte is the conferece number of this
                message. This byte can (and should) be
                ignored as it is duplicated in the message
                header in MESSAGES.DAT. This is especially
                important for conferences numbered higher
                than 255.
```

Let's stray just a moment to talk about the MSBIN floating point format.
This is the format used by the older Microsoft Basic compilers and
interpreters. Most compiler manufacturers have switched to the more
efficient IEEE floating point format. Therefore, we must have a method
of converting to and from MSBIN format. Included at the end of this
article are two routines in C that accomplish this quite easily.

Ok, let's talk about the format of the **.REP** (reply) packet. Like the
.QWK packet it is usually compressed. Inside the compressed archive is a
file whose extension is **.MSG.** The filename itself is the same as
line #5 of CONTROL.DAT. This is the BBSID. So, for example, if the BBSID
is GENERIC, the complete filename in the .REP packet would be GENERIC.MSG.

The format of the .MSG file is almost exactly the same as the
MESSAGES.DAT file with three differences:

1. In the first record, rather than a copyright notice, the first eight
bytes are the BBSID as described above. The rest of the record is filled
with spaces.

2. In the message header, rather than the ASCII-coded message number,
we have the ASCII-coded conference number

3. Also in the message header, the conference number field (byte offset
124 & 125) may be filled with spaces *OR* the conference number.

In recent months a new file, **DOOR.ID**, has been added to the .QWK
packet. I know very little about it but will attempt to explain it as
best as I can.

DOOR.ID seems to be a method for individual doors to let the mail reader
know how to add and drop conferences. It is a good idea and I hope more
doors and readers can be made to cooperate with it.

Usually there are only five lines in this file. Here is a sample from
one of my recent .QWK packets:

```
DOOR = TomCat!                       Line #1
VERSION = 2.9                        Line #2
SYSTEM = Wildcat! 2.x                Line #3
CONTROLNAME = TOMCAT                 Line #4
CONTROLTYPE = ADD                    Line #5
CONTROLTYPE = DROP                   Line #6
```

1. This is the mail door's name.
2. This is the mail door's version number.
3. This is the BBS software used and version number.
4. This is the control name (TO:) where to send requests for conference
   changes.
5. This is the command the door expects to see to add a conference to
   the user's current list.
6. This is the command the door expects to see to drop a conference from
   the user's current list.

Here are the routines I use to convert to and from the MSBIN format. You
may use them as you see fit - they are not copyrighted by me.


MSBIN conversion routines
-------------------------

```
union Converter
      {
       unsigned char uc[10];
       unsigned int  ui[5];
       unsigned long ul[2];
       float          f[2];
       double         d[1];
      }

/* MSBINToIEEE - Converts an MSBIN floating point number */
/*               to IEEE floating point format           */
/*                                                       */
/*  Input: f - floating point number in MSBIN format     */
/* Output: Same number in IEEE format                    */

float MSBINToIEEE(float f)
{
   union Converter t;
   int sign, exp;       /* sign and exponent */

   t.f[0] = f;

/* extract the sign & move exponent bias from 0x81 to 0x7f */

   sign = t.uc[2] / 0x80;
   exp  = (t.uc[3] - 0x81 + 0x7f) & 0xff;

/* reassemble them in IEEE 4 byte real number format */

   t.ui[1] = (t.ui[1] & 0x7f) | (exp << 7) | (sign << 15);
   return t.f[0];
} /* End of MSBINToIEEE */


/* IEEEToMSBIN - Converts an IEEE floating point number  */
/*               to MSBIN floating point format          */
/*                                                       */
/*  Input: f - floating point number in IEEE format      */
/* Output: Same number in MSBIN format                   */

float IEEEToMSBIN(float f)
{
   union Converter t;
   int sign, exp;       /* sign and exponent */

   t.f[0] = f;

/* extract sign & change exponent bias from 0x7f to 0x81 */

   sign = t.uc[3] / 0x80;
   exp  = ((t.ui[1] >> 7) - 0x7f + 0x81) & 0xff;

/* reassemble them in MSBIN format */

   t.ui[1] = (t.ui[1] & 0x7f) | (sign << 7) | (exp << 8);
   return t.f[0];
} /* End of IEEEToMSBIN */
```

Well, that is all there is to it! I hope this article has shed some
light on the so-called "mysterious" .QWK format.

Jeffery Foy, April 1991
