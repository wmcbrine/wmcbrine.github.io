QWK Mail Packet File Layout
===========================

by Patrick Y. Lee

**Version 1.6 - December 19, 1992**
: Added off-line commands for QSO (door for TBBS), thanks to
  message from Bob Hartman in the FidoNet Off-line echo.  All
  8-bit characters have been replaced with either blanks or
  equivalent 7-bit characters so I can send this file easily via
  Internet email.  Line 9 of the CONTROL.DAT file seems to be
  used by a few doors to indicate the NetMail conference.

**Version 1.5 - July 30, 1992**
: Added off-line commands for Cam-Mail door.  Fixed error in the
  status flag section.  The descriptions for '*' and '+' are
  incorrect.  Thanks to Bob Blaylock for bringing this up.

**Version 1.4 - July 18, 1992**
: Fixed a few minor mistakes in the documentation (thanks to
  Cody Gibson).  Nothing really major.  Also completed the
  Netmail section of the documentation.

**Version 1.3 - July 6, 1992**
: Added changes to the QWK format adopted by Qmail door.
  Specifically line 10 of CONTROL.DAT file and bytes 126-127 of
  MESSAGES.DAT file.  Please refer to the appropriate section
  for the changes.

**Version 1.2 - May 31, 1992**
: Added a few items to the DOOR.ID file that is being supported
  by Qmail DeLuxe2 version 1.25.

**Version 1.1 - March 15, 1992**
: Minor fixes here and there to make everything just right.

**Version 1.0 - February 23, 1992**
: First release.

This document is Copyright 1992 by Patrick Y. Lee.

The QWK-format is Copyright 1987 by Sparkware.

All program names mentioned in this document are either Copyright
or Trademark of respective owners.

The author provides this file as-is without warranty of any kind, either
expressed or implied.  You are using the information in this file at
your own discretion.  The author assumes no responsibilities for
damages, either physically or financially, from the use of this
information.

This document may be freely distributed by any means (electronically,
paper, etc.), provided that it is distributed in its entirety. Portions
of this document may be reproduced without credit.

---


Table of Contents
-----------------

- [1.  Introduction][10]
    - [1.1.  Intent][11]
    - [1.2.  History][12]
    - [1.3.  Questions, corrections, etc.][13]
- [2.  Conventions & overview][20]
    - [2.1.  The BBS ID][21]
    - [2.2.  Packet compression][22]
    - [2.3.  Packet transfer & protocols][23]
    - [2.4.  Limitations][24]
- [3.  QWK files][30]
    - [3.1.  Naming convention][31]
    - [3.2.  Control file (CONTROL.DAT)][32]
    - [3.3.  Welcome file][33]
    - [3.4.  Goodbye file][34]
    - [3.5.  News file][35]
    - [3.6.  Qmail DeLuxe2 menu file][36]
    - [3.7.  New uploads listing (NEWFILES.DAT)][37]
    - [3.8.  Bulletin file(s) (BLT-x.y)][38]
    - [3.9.  Message file (MESSAGES.DAT)][39]
    - [3.10.  Index files (*.NDX)][310]
        - [3.10.1.  Conference indices][3101]
        - [3.10.2.  Personal index (PERSONAL.NDX)][3102]
    - [3.11.  Pointer file][311]
    - [3.12.  SESSION.TXT][312]
- [4.  REP files][40]
    - [4.1.  Naming convention][41]
    - [4.2.  Message file (BBSID.MSG)][42]
    - [4.3.  Door control messages][43]
        - [4.3.1.  DOOR.ID file][431]
        - [4.3.2.  Qmail][432]
        - [4.3.3.  MarkMail][433]
        - [4.3.4.  KMail][434]
        - [4.3.5.  RoseMail][435]
        - [4.3.6.  Complete Mail Door][436]
        - [4.3.7.  The MainMail System][437]
        - [4.3.8.  BGQWK][438]
        - [4.3.9.  UltraBBS][439]
        - [4.3.10.  TriMail][4310]
        - [4.3.11.  Cam-Mail][4311]
        - [4.3.12.  QSO][4312]
        - [4.3.13.  TGWave][4313]
    - [4.4.  Turning off the echo flag][44]
    - [4.5.  Tag-lines][45]
- [5.  Net mail][50]
- [A.  Credits & contributions][a0]
- [B.  Sample Turbo Pascal and C code][b0]
- [C.  Sample message][c0]
- [D.  Sample index file][d0]

---


[1]  Introduction
-----------------

### [1.1]  Intent

This document is written to facilitate programmers who want to write
QWK-format mail doors or readers.  It is intended to be a comprehensive
reference covering all areas of QWK-format mail processing. Detailed
break down of each file is included, as are implementation information.
In addition, door and reader specific information may be included, when
such information are available to me.

### [1.2]  History

The QWK-format was invented by Mark "Sparky" Herring in 1987.  It was
based on Clark Development Corporation's PCBoard version 12.0 message
base format.  Off-line mail reading has become popular only in recent
years.  Prior to summer of 1990, there were only two QWK-format offline
mail reader programs.  They were Qmail DeLuxe by Mark Herring and
EZ-Reader by Eric Cockrell.  Similarly for the doors, there were only
two -- Qmail by Mark Herring and MarkMail by Mark Turner.  They were
both for PCBoard systems.

A lot has changed in both off-line reader and mail door markets since
summer 1990.  Now, there are more than a dozen off-line mail readers for
the PC.  Readers for the Macintosh, Amiga, and Atari exist as well.
There are over a half dozen doors for PCBoard, and QWK-format doors
exist for virtually all of the popular BBS softwares.  All of these
happened in less than two years!  More readers and doors are in
development as I write this, keep up the excellent work.  In addition to
doors, some BBS softwares has QWK-format mail facility built in.

Off-line mail reading is an integral part of BBS calling.  Conference
traffic and selection on all networks have grown dramatically in re-
cent years that on-line reading is a thing of the past.  Off-line mail
reading offers an alternative to reading mail on-line -- It offers speed
that cannot be achieved with on-line mail reading.

The reason why QWK-format readers and doors seem to have gained popu-
larity is probably dued to its openness.  The format is readily avail-
able to any programmer who wishes to write a program that utilize it.
Proprietary is a thing of the past, it does not work!  Openness is here
to stay and QWK-format is a part of it.

### [1.3]  Questions, corrections, etc.

Most of the message networks today have a conference / echo devoted to
discussion of off-line readers and mail doors.  The ones I know are on
FidoNet, ILink, Intelec, and RIME.  If you have questions after reading
anything in here, feel free to drop by any of the above conference /
echo and I am sure other QWK authors will try to help.


[2]  Conventions & overview
---------------------------

All offsets referenced in this document will be relative to 1.  I am not
a computer, I start counting at one, not zero!

Words which are enclosed in quotes should be entered as-is.  The
quotations are not part of the string unless noted.

You may have noticed I use the phrase "mail program" or "mail facility"
instead of mail doors.  This is because some BBS softwares offer the
option of creating QWK-format mail packets right from the BBS. With
those, there is no need for an external mail door.

### [2.1]  The BBS ID

The BBS ID (denoted as BBSID) is a 1-8 characters word that identifies
a particular BBS.  This identifier should be obtained from line 5 of
the CONTROL.DAT file (see section 3.2.1).

### [2.2]  Packet compression

Most mail packets are compressed when created by the mail door in order
to save download time and disk space.  However, many offline reader
programs allow the user to unarchive a mail packet outside of the reader
program, so the reader will not have to unarchive it.  Upon exit, the
reader will not call the archiver to save it.  It is up to the user to
archive the replies.  This is useful if the user has limited memory and
cannot shell out to DOS to run the unarchive program. For readers based
on non-PC equipment, the user may be using less common compression
program that does not have command line equivalent.

### [2.3]  Packet transfer & protocols

There is no set rule on what transfer protocol should be used.  However,
it would be nice for the mail program on the BBS to provide the Sysop
with options as to what to offer.  This should be a configuration option
for the user.

### [2.4]  Specifications & limitations

There aren't many known limits in the QWK specification.  However,
various networks seem to impose artificial limits.  On many of the
PC-based networks, 99-lines appears to be the upper limit for some
softwares.  However, most of the readers can handle more than that.
Reader authors reading this may want to offer the option to split
replies into n lines each (the actual length should be user definable so
when the network software permits, the user can increase this number).


[3]  QWK files
--------------

### [3.1]  Naming convention

Generally, the name of the mail packet is BBSID.QWK.  However, this does
not have to be the case.  When the user downloads more than one mail
packet at one time, either the mail program or the transfer protocol
program will rename the second and subsequent mail packets to other
names.  They will either change the file extension or add a number to
the end of the filename.  In either case, you should not rely on the
name of the QWK file as the BBSID.  The BBSID, as mentioned before,
should be obtained from line 5 of the CONTROL.DAT file. In addition,
mail packets do not have to end with QWK extension either.  The user may
choose to name them with other file extensions.

### [3.2]  Control file (CONTROL.DAT)

The CONTROL.DAT file is a simple ASCII file.  Each line is terminated
with a carriage return and line feed combination.  All lines should
start on the first column.

```
Line #
 1   My BBS                   BBS name
 2   New York, NY             BBS city and state
 3   212-555-1212             BBS phone number
 4   John Doe, Sysop          BBS Sysop name
 5   20052,MYBBS              Mail door registration #, BBSID
 6   01-01-1991,23:59:59      Mail packet creation time
 7   JANE DOE                 User name (upper case)
 8                            Name of menu for Qmail, blank if none
 9   0                        ? Seem to be always zero.  A few doors,
                              such as DCQWK for TAG is using this
                              field to indicate the conference where
                              Fido NetMail should be placed.  This
                              gives the reader program the ability
                              easily send NetMail.
10   999                      Total number of messages in packet
11   121                      Total number of conference minus 1
12   0                        1st conf. number
13   Main Board               1st conf. name (13 characters or less)
14   1                        2nd conf. number
15   General                  2nd conf. name
..   3                        etc. onward until it hits max. conf.
..   123                      Last conf. number
..   Amiga_I                  Last conf. name
..   HELLO                    Welcome screen file
..   NEWS                     BBS news file
..   SCRIPT0                  Log off screen
```

Some mail doors, such as MarkMail, will send additional information
about the user from here on.

```
0                             ?
25                            Number of lines that follow this one
JANE DOE                      User name in uppercase
Jane                          User first name in mixed case
NEW YORK, NY                  User city information
718 555-1212                  User data phone number
718 555-1212                  User home phone number
108                           Security level
00-00-00                      Expiration date
01-01-91                      Last log on date
23:59                         Last log on time
999                           Log on count
0                             Current conference number on the BBS
0                             Total KB downloaded
999                           Download count
0                             Total KB uploaded
999                           Upload count
999                           Minutes per day
999                           Minutes remaining today
999                           Minutes used this call
32767                         Max. download KB per day
32767                         Remaining KB today
0                             KB downloaded today
23:59                         Current time on BBS
01-01-91                      Current date on BBS
My BBS                        BBS network tag-line
0                             ?
```

Some mail doors will offer the option of sending an abbreviated
conference list.  That means the list will contain only conferences the
user has selected.  This is done because some mail readers cannot handle
more than n conferences at this time.  Users using those readers will
need this option if the BBS they call have too many conferences.

### [3.3]  Welcome file

This file usually contains the log on screen from the BBS.  The exact
filename is specified in the CONTROL.DAT file, after the conference
list.  This file may be in any format the Sysop chooses it be -- usually
either in plain ASCII or with ANSI screen control code.  Some Sysops
(notably PCBoard Sysops) may use BBS-specific color change code in this
file as well.  Current mail programs seem to handle the translations
between BBS-specific code to ANSI based screen control codes.

Even if the CONTROL.DAT file contains the filename of this file, it may
not actually exist in the mail packet.  Sometimes, users will manually
delete this file before entering the mail reader.  Some offline readers
offer the option to not display this welcome screen; some will display
this file regardless.  Some doors, similarly, will offer option to the
user to not send this file.

### [3.4]  Goodbye file

Similar to the welcome file above, the filename to the goodbye file is
in the CONTROL.DAT file.  This is the file the BBS displays when the
user logs off the board.  It is optional, as always, to send this file
or to display it.

### [3.5]  News file

Many mail doors offer the option to send the news file from the BBS.
Most will only send this when it has been updated.  Like the welcome and
goodbye files, the filename to the news file is found in the CONTROL.DAT
file.  It can be in any format the Sysop chooses, but usually in either
ASCII or ANSI.  Like the welcome screen, current mail facilities seem to
handle translation between BBS-specific control codes to ANSI screen
control codes.

### [3.6]  Qmail DeLuxe2 menu file

This file is of use only for Qmail DeLuxe2 mail reader by Sparkware. The
filename is found on line 8 of the CONTROL.DAT file.

### [3.7]  New uploads listing (NEWFILES.DAT)

Most mail programs on the BBS will offer the option to scan new files
uploaded to the BBS.  The result is found in a file named NEWFILES.DAT.
The mail program, if implementing this, should update the last file scan
field in the user's profile, if there is such a field, as well as other
information required by the BBS.  The mail program should, of course,
scan new files only in those areas the user is allowed access.

### [3.8]  Bulletin files (BLT-x.y)

Most mail programs will also offer the option to include updated
bulletin files found on the BBS in the mail packet.  The bulletins are
named BLT-x.y, where x is the conference / echo the bulletin came from,
and y the bulletin's actual number.  The mail program will have to take
care of updating the last read date on the bulletins in the user record.

### [3.9]  Message file (MESSAGES.DAT)

The MESSAGES.DAT file is the most important.  This is where all of the
messages are contained in.  The QWK file format is based on PCBoard 12.0
message base format from Clark Development Corporation (maker of PCBoard
BBS software).

The file has a logical record length of 128-bytes.  The first record of
MESSAGES.DAT always contain a copyright notice saying "Produced by
Qmail...Copyright (c) 1987 by Sparkware.  All Rights Reserved".  The
rest of the record is space filled.  Actual messages consist of a
128-bytes header, plus one or more 128-bytes block with the message
text. Actual messages start in record 2.  The header block is layed out
as follows:


```
Offset  Length  Description
------  ------  ----------------------------------------------------
    1       1   Message status flag (unsigned character)
                ' ' = public, unread
                '-' = public, read
                '*' = private, read by someone but not by intended
                recipient
                '+' = private, read by official recipient
                '~' = comment to Sysop, unread
                '`' = comment to Sysop, read
                '%' = sender password protected, unread
                '^' = sender password protected, read
                '!' = group password protected, unread
                '#' = group password protected, read
                '$' = group password protected to all
    2       7   Message number (in ASCII)
    9       8   Date (mm-dd-yy, in ASCII)
   17       5   Time (24 hour hh:mm, in ASCII)
   22      25   To (uppercase, left justified)
   47      25   From (uppercase, left justified)
   72      25   Subject of message (mixed case)
   97      12   Password (space filled)
  109       8   Reference message number (in ASCII)
  117       6   Number of 128-bytes blocks in message (including the
                header, in ASCII; the lowest value should be 2, header
                plus one block message; this number may not be left
                flushed within the field)
  123       1   Flag (ASCII 225 means message is active; ASCII 226
                means this message is to be killed)
  124       2   Conference number (unsigned word)
  126       2   Logical message number in the current packet; i.e.
                this number will be 1 for the first message, 2 for the
                second, and so on. (unsigned word)
  128       1   Indicates whether the message has a network tag-line
                or not.  A value of '*' indicates that a network tag-
                line is present; a value of ' ' (space) indicates
                there isn't one.  Messages sent to readers (non-net-
                status) generally leave this as a space.  Only network
                softwares need this information.
```

Fields such as To, From, Subject, Message #, Reference #, and the like
are space padded if they are shorter than the field's length.

The message text starts in the next record.  You can find out how many
blocks make up one message by looking at the value of "Number of 128
byte blocks".  Instead of carriage return and line feed combination,
each line in the message end with an ASCII 227 (pi character) symbol.
There are reports that some (buggy) readers have problems with messages
which do not end the last line in the message with an ASCII 227. If a
message does not completely occupy the 128-bytes block, the remainder of
the block is padded with space or null.

Note that there seems to exist old doors which will use one byte to
represent the conference number and pad the other one with an ASCII 32
character.  The program reading this information will have to determine
whether the ASCII 32 in byte 125 of the header is a filler or part of
the unsigned word.  One method is to look in the CONTROL.DAT file to
determine the highest conference number.

Even though most mail programs will generate MESSAGES.DAT files that
appear in conference order, this is not always the case.  Tomcat! (mail
door for Wildcat! BBS) generates MESSAGES.DAT that is not in conference
order.  This is due to how Wildcat! itself stores mail on the BBS.
Another known system that behaves this way is Auntie, which has its mail
door QWiKer.

Note that some mail doors offer the option of sending a mail packet even
though there may be no messages to send -- thus an empty MESSAGES.DAT
file.  This was tested with Qmail 4.0 door and it sent a MESSAGES.DAT
file that contains a few empty 128-bytes blocks.  Other mail doors seem
to be able to produce QWK files without the MESSAGES.DAT file at all!
Apparently, there was no standard established in this procedure.

### [3.10]  Index files (*.NDX)

#### [3.10.1]  Conference indices

The index files contain a list of pointers pointing to the beginning of
messages in the MESSAGES.DAT file.  The pointer is in terms of the
128-bytes block logical record that the MESSAGES.DAT file is in.  Each
conference has its own xxx.NDX file, where xxx is the conference number
left padded with zeroes.  Some mail programs offer the user the option
to not generate index files.  So the mail readers need to create the
index files if they are missing.

EZ-Reader 1.xx versions will convert the NDX files from Microsoft MKS
format into IEEE long integer format.  The bad part about this is that
the user may store those index files back into the QWK file.  When
another reader reads the index files, it will be very confused!

Special note for BBSes with more than 999 conferences: Index files for
conferences with four digit conference numbers is named xxxx.NDX, where
xxxx is the conference number (left padded with zeroes).  The filenames
for three digit conferences are still named xxx.NDX on these boards.  I
would assume filenames for conferences in the five digit range is
xxxxx.NDX, but I have not seen a BBS with 10,000 or more conferences
yet!

Each NDX file uses a five bytes logical record length and is formatted
to:

```
Offset  Length  Description
------  ------  ------------------------------------------------------
    1       4   Record number pointing to corresponding message in
                MESSAGES.DAT.  This number is in the Microsoft MKS$
                BASIC format.
    5       1   Conference number of the message.  This byte should
                not be used because it duplicates both the filename of
                the index file and the conference # in the header.  It
                is also one byte long, which is insufficient to handle
                conferences over 255.
```

Please refer to appendix B for routines to deal with MKS numbers.

#### [3.10.2]  Personal index (PERSONAL.NDX)

There is a special index file named PERSONAL.NDX.  This file contains
pointers to messages which are addressed to the user, i.e. personal
messages.  Some mail door and utility programs also allow the selection
of other messages to be flagged as personal messages.

### [3.11]  Pointer file

Pointer file is generally included so that the user can reset the last
read pointers on the mail program, in case there is a crash on the BBS
or some other mishaps.  There should be little reason for the reader
program to access the pointer file.

The pointer files I have seen are:

    KMail          BBSID.PNT
    MarkMail       BBSID.PNT
    Qmail          BBSID.PTR
    QWiKer         HMP.PTR
    SFMailQwk      BBSID.SFP

Additions to this list are welcomed.

### [3.12]  SESSION.TXT

This file, if included, will contain the message scanning screen the
user sees from the door.


[4]  REP files
--------------

### [4.1]  Naming convention

The reply file is named BBSID.MSG, where BBSID is the ID code for the
BBS found on line 5 of the CONTROL.DAT file.  Once this file has been
created, the mail reader can archive it up into a file with REP
extension.

### [4.2]  Message file (BBSID.MSG)

Replies use the same format as the MESSAGES.DAT file, except that
message number field will contain the conference number instead.  In
other words, the conference number will be placed in the two bytes
(binary) starting at offset 124, as well as the message number field
(ASCII) at offset 2.

A private message is indicated by '*' in the message status flag.  For
some reason, most mail doors only accept '*' as a private message and
not '+'.

The first 128-bytes record of the file is the header.  Instead of the
copyright notice, it contains the BBSID of the BBS.  This 1-8 character
BBSID must start at the very first byte and must match what the BBS has.
The rest of the record is space padded.  The replies start at record 2.
Each reply message will have a 128-bytes header, plus one or more for
the message text; followed by another header, and so on.

The mail program must check to make sure the BBSID in the first block of
the BBSID.MSG file matches what the BBS has!

### [4.3]  Door control messages

These messages allow the user to change their setup on the BBS by simply
entering a message.  The goal is to allow the user to be able to control
most areas of the BBS via the mail door.  Different mail doors have
different capabilities.  Most all of them offer the ability to add and
drop a conference, as well as reset the last read pointers in a
conference.

There are two trends being developed for door control messages.  The one
being referred to as the "old" style accepts one command per message
(command entered in the subject line); whereas the "new" style accepts
multiple commands per message (commands entered in the message body).
The old style is by far the more popular but more and more doors are
beginning to offer both.

#### [4.3.1]  DOOR.ID file

The DOOR.ID file was first introduced by Greg Hewgill with Tomcat! mail
door and SLMR mail reader.  Since then, many other authors have picked
up this idea and use the format.  This file provides the necessary
identifiers a reader needs to send add, drop, etc. messages to the mail
door.  It tells the reader who to address the message to and what can be
put in the subject line.

```
DOOR = <doorname>             This is the name of the door that created
                              the QWK packet, i.e. <doorname> = Tomcat.
VERSION = <doorversion>       This is the version number of the door
                              that created the packet, i.e.
                              <doorversion> = 2.9.
SYSTEM = <systemtype>         This is the underlying BBS system type
                              and version, i.e. <systemtype> = Wildcat
                              2.55.
CONTROLNAME = <controlname>   This is the name to which the reader
                              should send control messages, eg.
                              <controlname> = TOMCAT.
CONTROLTYPE = <controltype>   This can be one of ADD, DROP, REQUEST,
                              or others.  ADD and DROP are pretty
                              obvious (they work as in MarkMail), and
                              REQUEST is for use with BBS systems that
                              support file attachments.  Try out SLMR
                              with CONTROLTYPE = REQUEST and use the Q
                              function.  (This seems to be a Wildcat!
                              BBS feature.)
RECEIPT                       This flag indicates that the door/BBS is
                              capable of return receipts when a message
                              is received.  If the first three letters
                              of the subject are RRR, then the
                              door should strip the RRR and set the
                              'return-receipt-requested' flag on the
                              corresponding message.  When the addressee
                              reads this message, the BBS generates a
                              message with the date/time it was read.
MIXEDCASE = YES               If this line is found then the reader
                              will let you use upper and lower case
                              names and subjects.  This is first found
                              in Qmail DeLuxe2 1.25 version.  Most
                              other QWK readers permit the use of
                              mixed case subject lines but force the
                              names to upper case only.
FIDOTAG = YES                 If this line is found then the reader
                              will automatically use FidoNet compliant
                              tag-lines.
```

None of the lines are actually required and they may appear in any
order.  Of course, you would need a CONTROLNAME if you have any
CONTROLTYPE lines.

#### [4.3.2]  Qmail

Qmail uses the "new" style of control message; therefore, send a message
addressed to "QMAIL" with a subject of "CONFIG".  Then, enter any of the
commands listed below inside the text of your message.  Remember to use
one command per line.

```
ADD <confnum>            Add a conference into the Qmail Door scanning
                         list.  "YOURS" can also be added to the command
                         if the user wishes to receive messages only
                         addressed them.  i.e. "ADD 1 YOURS". "YOURS ALL"
                         can be added to select a conference for all
                         mailed addressed to the user and to "ALL".
DROP <confnum>           Drop a conference from the Qmail Door scanning
                         list.  "DROP ALL" wipes the slate clean.
RESET <confnum> <value>  Resets a conference to a particular value.
                         The user can use "HIGH-xxx" to set the
                         conference to the highest message in the base.
CITY <value>             Changes the "city" field in the user's
                         PCBoard entry.
PASSWORD <value>         Changes the user's login password.
BPHONE <value>           Business/data phone number
HPHONE <value>           Home/voice phone number
PCBEXPERT <on|off>       Turns the PCBoard expert mode ON or OFF.
PCBPROT <value>          PCBoard file transfer protocol (A-Z).
PAGELEN <value>          Set page length inside PCBoard.
PCBCOMMENT <value>       Set user maintained comment.
AUTOSTART <value>        Qmail Door autostart command.
PROTOCOL <value>         Qmail Door file transfer protocol (A-Z).
EXPERT <ON or OFF>       Turns the Qmail Door expert mode ON or OFF.
MAXSIZE <value>          Maximum size of the user's .QWK packet (in
                         bytes)
MAXNUMBER <value>        Maximum number of messages per conference.
SCANDATE <value>         Changes the date of the last file directory
                         scan to <value>
```

An example of a message would be:

    ADD 1
    ADD 3-10
    DROP 18
    DROP 20-30
    RESET 2 HIGH-20
    RESET 3-10 HIGH-100
    OPTION 29 ON
    PASSWORD F3GUM
    PAGELEN 24

Qmail Door 4.00 will report back to you the results of these
configuration changes when you upload the reply packet to the Qmail
Door.

#### [4.3.3]  MarkMail

Send a message addressed to "MARKMAIL" with the subject line saying:

```
ADD [value]         in the conference you want to add
DROP                in the conference you want to drop
YOUR [value]        in the conference you want only your mail sent
YA [value]          in the conference you want only your mail + mail
                    addressed to "ALL"
FILES ON or OFF     in any conference to tell MarkMail whether to scan
                    for new files or not.
BLTS ON or OFF      to turn on and off, respectively, of receiving
                    bulletins.
OWN ON or OFF       to turn on and off, respectively, of receiving
                    messages you sent
DELUXE ON or OFF    to turn on and off, respectively, of receiving
                    DeLuxe menu
LIMIT <size>        to set the maximum size of MESSAGES.DAT file can
                    be, it cannot exceed what the Sysop has set up
```

An optional number can be added onto the commands "ADD", "YOUR", and
"YA".  If this number is positive, then it will be treated as an
absolute message number.  MarkMail will set your last read pointer to
that number.  If it is negative, MarkMail will set your last read
pointer to the highest minus that number.  For example: "ADD -50" will
add the conference and set the last read pointer to the highest in the
conference minus 50.

#### [4.3.4]  KMail

Send a private message addressed to "KMAIL" in the conference that you
want to add, drop, or reset.  The commands are "ADD", "DROP", and
"RESET #", respectively.  The "#" is the message number you want your
last read pointer in the conference be set to.

#### [4.3.5]  RoseMail

The RoseMail door allows configuration information be placed in either
the subject line or message text.  The message must be addressed to
"ROSEMAIL".  For only one command, it can be placed in the subject line.
For more than one changes, the subject line must say "CONFIG" and each
change be placed in the message text.  Every line should be left
justified.  Valid commands are:

```
Command                                           Example
------------------------------------------------- ------------------
ADD <Conference> [<Message #>] [<Yours>]          ADD 2 -3 Y
DROP <Conference>                                 DROP 2
RESET <Conference> <Message #>                    RESET 12 5000
PCBEXPERT <ON | OFF> - PCBoard expert mode        PCBEXPERT ON
EXPERT <ON | OFF>    - RoseMail expert mode       EXPERT OFF
PCBPROT <A - Z>      - PCBoard protocol           PCBPROT Z
PROT <A - Z>         - RoseMail protocol          PROT G
PAGELEN <Number>     - Page length                PAGELEN 20
MAXSIZE <Kbytes>     - Max packet size in Kb      MAXSIZE 100
MAXNUMBER <max msgs/conference>                   MAXNUMBER 100
JUMPSTART <Sequence or OFF>                       JUMPSTART D;Y;Q
MAXPACKET <max msgs/packet>                       MAXPACKET 500
AUTOSTART <Sequence or OFF> - same as jumpstart   AUTOSTART OFF
OPT <##> <ON | OFF>  - set door option            OPT 2 OFF
```

#### [4.3.6]  Complete Mail Door

Send message to "CMPMAIL", the commands are "ADD" and "DROP".  This
message must be sent in the conference that you want to add or drop.

#### [4.3.7]  The MainMail System

Send a message addressed to "MAINMAIL" with the subject line saying:

```
ADD [value]         in the conference you want to add
DROP                in the conference you want to drop
YOURS [value]       in the conference you want only your mail sent
YOURS ALL [value]   in the conference you want only your mail + mail
                    addressed to "ALL"
```

The optional [value] parameter functions identically to the MarkMail
door -- positive number indicates an absolute message number, negative
number will set your last read pointer that many messages from the last
message.

#### [4.3.8]  BGQWK

The BGQWK mail door for GT Power supports file request via message. To
request a file, simply enter a message:

```
     To: BGQWK
Subject: DL:FILENAME.EXT
```

The FILENAME.EXT has to be exact name, wildcard is not supported.  The
message text can be left blank.

The only limit on the file request feature is your time and/or your file
ratio.  The transfer of the requested file(s) will start right after the
QWK download is completed.  The only exception is when the user is using
a bidirectional transfer protocol.  There, the REP and QWK are sent at
the same time, hence, the file request cannot be processed until the QWK
transfer is completed.

#### [4.3.9]  UltraBBS

Send a private message addressed to "ULTRABBS" in the conference that
you want to add, drop, or reset.  The commands are "ADD", "DROP", and
"RESET #", respectively.  The "#" is the message number you want your
last read pointer in the conference be set to.  The QWK mail door for
UltraBBS is built in and it generates the DOOR.ID file as well.

#### [4.3.10]  TriMail

TriMail is the QWK door for TriBBS.  This door will accept off-line
configuration options sent to Qmail, MarkMail, or TriMail.  This means
you can send the message to: "QMAIL", "MARKMAIL", or "TRIMAIL" in the
appropriate conference and it will work.  The available options are:

```
BLTS ON             Turn bulletin scan on
BLTS OFF            Turn bulletin scan off
FILES ON            Turn new files scan on
FILES OFF           Turn new files scan off
RESET <value>       Reset last message read pointer
DROP                Drop this conference
ADD [value]         Add this conference.  The [value] is optional and
                    will set the last message read pointer.
```

#### [4.3.11]  Cam-Mail

Address your message to either QMAIL or CAM-MAIL.  Then enter the
command in the subject line.  Valid commands are:

```
Add                 Add conferences for scanning
BLTS On             Download all new bulletins in QWK packet
BLTS Off            Turn off download of new bulletins
Color On            Download the QWK packet in color, if available
Color Off           Turn off download in color
Drop                Drop a conference from scanning
Duplicates On       Turn on duplicate checking in REP files
Duplicates Off      Turn off duplicate checking
Files On            Download new files list in QWK packet
Files Off           Turn off download of new files list
Goodbye On          Send the Goodbye files with the QWK packet
Goodbye Off         Does not send the Goodbye files
Mailflags On        Notifies users they have mail
Mailflags Off       Does not notify users of mail
NDX On              Create NDX files for QWK packet
NDX Off             Does not create NDX files
Welcome On          Send the Welcome file with the QWK packet
Welcome Off         Does not send the Welcome file
```

Cam-Mail Gold adds the following commands:

```
AddY                Adds conference in "YOUR" mail mode
AddB                Adds conference in "YOUR and ALL" mail mode
```

#### [4.3.12]  QSO

QSO supports both "old" and "new" style off-line configuration commands.
For the "old" style, message should be addressed to either QMAIL or QSO.
The message must be entered in the conference it affects, with the
subject line containing:

```
ADD                 Add conference
ADD -20             Add conference and set last read pointer to the
                    highest number in conference minus 20
ADD HIGH-20         Same as above
ADD LOW+50          Add conference and set last read pointer to the
                    lowest number in conference plus 50
ADD 1234            Add conference and set last read pointer to a
                    specific message number
ADD YOURS           Add conference for messages addressed to you only;
                    the YOURS keyword may appear before or after the
                    message number; YOURS may be abbreviated as just Y
DROP                Drop the conference
RESET               Reset last read pointer to highest in conference
RESET -20           Reset to highest number minus 20
RESET HIGH-20       Same as above
RESET LOW+50        Reset to lowest number plus 50
RESET 1234          Reset last read pointer to specific number
```

For the "new" style message, enter the message addressed to either
"QMAIL" or "QSO" with the subject line saying "CONFIG".  The message
text should have commands similar to the ones listed above.  The only
exception is that the conference number must be entered immediately
after the first keyword.  For example:

```
ADD 15 -20          Add conference 15, reset pointer to high minus 20
DROP 137            Drop conference 137
ADD 20 HIGH-50 Y    Add conference 20 for mail addressed to user only,
                    and set the pointer to highest minus 50
```

QSO supports file sending in the mail packet as well.  Attached files
have the name EMnnnnn, where nnnnn is a five digit leading zero padded
number indicating the message number in which the request was sent.
The plain text file ATTACHED.LST lists the real name of the file, and
has the following format (one line per record):

    <conference #>, <message #>, <EMnnnnn filename>, <real name>

    <conference #>      Conference where the request came from
    <message #>         Message number where request came from
    <EMnnnnn filename>  File as named inside the QWK file
    <real name>         Original filename as seen on the BBS

For messages that has an enclosed file, the last line of the message
has the line:

*Enclosed file: filename.ext

The reader program must send a request to obtain this file.  The request
is in the format similar to any other control messages:

```
To: QSO
Subject: REQUEST nnnnn
```

where nnnnn is the message number that contains this attached file. This
message must be sent in the conference where that contains that message.
Alternatively, the "new" style can also be used.  With the line "REQUEST
confnum nnnnn" in the message text.

To upload a file that is attached to a message, the file ATTXREF.DAT
must be created.  The file is a plain text file with one line
corresponding to each message in the reply file.  Blank lines must be
included for those messages that do not have files attached to them.  A
non-blank line has the following format to indicate a file is attached
to that message:

    <internal name>, <real name>

    <internal name>     is any non-conflicting filename
    <real name>         the name as it should appear on the BBS

#### [4.3.13]  TGWave

Send config messages in the area the changes are to take effect.  In
addition to normal ADD, DROP, RESET commands, the door responds to the
following config messages:

    "ADD YOURS" ┐  Selects the area,
    "ADD Y"     ├  toggles it to personal
    "YOURS"     ┘  messages only.

    "ADD YA"    ┐  Selects the area, toggles it
    "YOURS ALL" ├  to personal messages, and messages
    "YA"        ┘  to "All" only.

TGWave processes QWK offline configuration requests that are addressed
to one of the following names: "TELEGARD", "QMAIL", "ROSEMAIL" (case-
insensitive), in addition to "TGWAVE".


### [4.4]  Turning off the echo flag

In order to send a non-echoed message (not send out to other BBSs), a
user can enter "NE:" in front of the subject line.  This feature may not
be offered in all mail doors.

Some mail doors strip out the "NE:" from the subject line.  However,
that leaves the recipient with nothing to tell that the message was not
echoed.  Therefore, it is better if "NE:" is not stripped.

### [4.5]  Tag-lines

The most common format for a reader tag-line is:

```
---
 * My reader v1.00 * The rest of the tag-line.
```

(The asterisks above should be replaced by IBM extended character
number 254 - a square block.)

The three dashes is called a tear-line.  The tag-line is appended to the
end of the message and is usually one line only.  It is preferred that
tag-lines conform to this format so that networking softwares such as
QNet and RNet will not add another tearline to the message when they
process it.

Softwares on FidoNet does not like mail readers adding a tear-line of
their own, so if your mail reader offers a FidoNet mode, you will need
to get rid of the tear-line.  Another item which differs between the
FidoNet and PC-based networks is that FidoNet does not like extended
ASCII characters.  So your reader may want to strip high ASCII if the
user has FidoNet mode on.  Acceptable tag-line style, I believe, is just
this:

```
 * My Reader v1.00 * The rest of the tag-line.
```


[5]  Net mail
-------------

QWK mail doors can be used along or in conjunction with QWK network
softwares to operate a network.  Many such network exists.  A Net-
Status packet (one that can be imported to BBS message base) is very
similar to a normal mail packet users get.  The only difference is at
the end of the MESSAGES.DAT file.  There, a series of 128 byte blocks
are appended to indicate Net-Status.

One byte represent one conference, in groups of 128.  A non-zero value
means the user is allowed Net Mail status for that conference.  The
number of Net Status blocks appended to the MESSAGES.DAT file only needs
to cover the highest conference the user has access to.

The reason these packets have to be different?  Using QWK mail doors to
send and receive messages require you to be able to send messages in
other user's name as well as receive private messages in conferences.
The Net-Status tells the mail door to import the messages even if they
are from another name.

The only weird implementation here is that the block with the highest
conference numbers come before the lower ones.  Hence, to illustrate:

```
       Conference 128
       |  Conference 129
       |  |
      vv vv
0000  00 00 FF 00 00 00 00 00-00 00 00 00 00 00 00 00  Flags for
0010  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00  conferences
0020  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00  128-255
0030  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
0040  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
0050  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
0060  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
0070  00 00 00 00 00 00 00 00-00 00 00 00 00 00 FF 00

       Conference 0
       |  Conference 1
       |  |
      vv vv
0080  00 FF 00 00 00 00 00 00-00 00 00 00 00 00 00 00  Flags for
0090  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00  conferences
00A0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00  0-127
00B0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
00C0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
00D0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
00E0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 00
00F0  00 00 00 00 00 00 00 00-00 00 00 00 00 00 00 FF
```

This example shows that this user has Net Status in conferences 1,
127, 130, and 254.

MarkMail and KMail doors (both for PCBoard) choos to implement things a
little differently.  Instead of appending Net-Status blocks to the end
of the MESSAGES.DAT file, they simply put "MarkMail" or "Kmail..." in
the first 8 position of the file and the user is granted Net-Status in
all conferences.

---


[A]  Credits and Contributions
------------------------------

Mark "Sparky" Herring, who originated the QWK-format.

Tim Farley, who started this documentation back in the summer of 1990.
The general outline here is the work of Tim.  I filled in the blanks.

Jeffery Foy, who gave us the format for Microsoft single binary versus
IEEE format.

Greg Hewgill, who (if I remember correctly) wrote the Turbo Pascal
routines (included in here) to convert between MKS and TP LongInt.

Dennis McCunney, who is the host of the Off-line conference on RIME,
is very knowledgeable in off-line reading concept and programs.  His
goal is to have one reader that can read mail packet from any source.

Cody Gibson and Jeffery Foy, whose information on Net-Status packet
are included here.

All those who have been around the Off-line conferences on ILink (the
oldest of the four I participate), RIME, Fido, and Intelec, who have
provided great help over the past three years.  The bulk of the
information presented here are from messages in those conferences.
These people include, but are no limited to, the followings: Dane Beko,
Joseph Carnage, Marcos Della, Joey Lizzi, Mark May, and Jim Smith.


[B]  Sample Turbo Pascal and C code
-----------------------------------

Here are a few routines in Turbo Pascal and C to convert Microsoft BASIC
MKS format to usable IEEE long integer.  These are collected over the
networks and there is no guarantee that they will work for you!

Turbo Pascal (Greg Hewgill ?):

```
type
     bsingle = array [0..3] of byte;

{ converts TP real to Microsoft 4 bytes single }

procedure real_to_msb (preal : real; var b : bsingle);
var
     r : array [0 .. 5] of byte absolute preal;
begin
     b [3] := r [0];
     move (r [3], b [0], 3);
end; { procedure real_to_msb }

{ converts Microsoft 4 bytes single to TP real }

function msb_to_real (b : bsingle) : real;
var
     preal : real;
     r : array [0..5] of byte absolute preal;
begin
     r [0] := b [3];
     r [1] := 0;
     r [2] := 0;
     move (b [0], r [3], 3);
     msb_to_real := preal;
end; { function msb_to_real }
```

Another Turbo Pascal routine to convert Microsoft single to TP LongInt
(Marcos Della):

```
index := ((mssingle and not $ff000000) or $00800000) shr (24 -
((mssingle shr 24) and $7f)) - 1;
```

C (identify yourself if you wrote this routine):

```
/* converts 4 bytes Microsoft MKS format to long integer */

unsigned long mbf_to_int (m1, m2, m3, exp)
unsigned int m1, m2, m3, exp;
{
     return (((m1 + ((unsigned long) m2 << 8) + ((unsigned long) m3 <<
     16)) | 0x800000L) >> (24 - (exp - 0x80)));
}
```

Microsoft binary (by Jeffery Foy):

```
   31 - 24    23     22 - 0        <-- bit position
+-----------------+----------+
| exponent | sign | mantissa |
+----------+------+----------+

IEEE (C/Pascal/etc.):

   31     30 - 23    22 - 0        <-- bit position
+----------------------------+
| sign | exponent | mantissa |
+------+----------+----------+
```

In both cases, the sign is one bit, the exponent is 8 bits, and the
mantissa is 23 bits.  You can write your own, optimized, routine to
convert between the two formats using the above bit layout.


[C]  Sample message
-------------------

Here is a sample message in hex and ASCII format:

```
019780  20 34 32 33 32 20 20 20 30 32 2D 31 35 2D 39 32   4232   02-15-92
019790  31 33 3A 34 35 52 49 43 48 41 52 44 20 42 4C 41  13:45RICHARD BLA
0197A0  43 4B 42 55 52 4E 20 20 20 20 20 20 20 20 53 54  CKBURN        ST
0197B0  45 56 45 20 43 4F 4C 45 54 54 49 20 20 20 20 20  EVE COLETTI
0197C0  20 20 20 20 20 20 20 51 45 44 49 54 20 48 41 43         QEDIT HAC
0197D0  4B 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20  K
0197E0  20 20 20 20 20 20 20 20 20 20 20 20 34 30 33 36              4036
0197F0  20 20 20 20 37 20 20 20 20 20 E1 0A 01 00 00 20      7
019800  2A 20 49 6E 20 61 20 6D 65 73 73 61 67 65 20 64  * In a message d
019810  61 74 65 64 20 30 32 2D 30 39 2D 39 32 20 74 6F  ated 02-09-92 to
019820  20 53 74 65 76 65 20 43 6F 6C 65 74 74 69 2C 20   Steve Coletti,
019830  52 69 63 68 61 72 64 20 42 6C 61 63 6B 62 75 72  Richard Blackbur
019840  6E 20 73 61 69 64 3A E3 E3 52 42 3E 53 43 20 AF  n said:  RB>SC
019850  20 65 64 69 74 6F 72 20 69 6E 20 74 68 65 20 28   editor in the (
019860  6D 61 69 6E 66 72 61 6D 65 29 20 56 4D 2F 43 4D  mainframe) VM/CM
019870  53 20 70 72 6F 64 75 63 74 20 6C 69 6E 65 20 69  S product line i
[ etc. ]
019A00  6E 6F 74 20 61 20 44 6F 63 74 6F 72 2C 20 62 75  not a Doctor, bu
019A10  74 20 49 20 70 6C 61 79 20 6F 6E 65 20 61 74 20  t I play one at
019A20  74 68 65 20 48 6F 73 70 69 74 61 6C 2E E3 20 20  the Hospital.
019A30  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
019A40  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
019A50  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
019A60  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
019A70  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
019A80  E3 50 43 52 65 6C 61 79 3A 4D 4F 4F 4E 44 4F 47   PCRelay:MOONDOG
019A90  20 2D 3E 20 23 33 35 20 52 65 6C 61 79 4E 65 74   -> #35 RelayNet
019AA0  20 28 74 6D 29 E3 34 2E 31 30 20 20 20 20 20 20   (tm) 4.10
019AB0  20 20 20 20 20 20 20 20 20 48 55 42 4D 4F 4F 4E           HUBMOON
019AC0  2D 4D 6F 6F 6E 44 6F 67 20 42 42 53 2C 20 42 72  -MoonDog BBS, Br
019AD0  6F 6F 6B 6C 79 6E 2C 4E 59 20 37 31 38 20 36 39  ooklyn,NY 718 69
019AE0  32 2D 32 34 39 38 E3 20 20 20 20 20 20 20 20 20  2-2498
019AF0  20 20 20 20 20 20 20 20 20 20 20 20 20 20 20 20
```


[D]  Sample index file
----------------------

Here is a sample index file in hex format:

```
000000  00 00 28 87 19 00 00 30 87 19 00 00 38 87 19 00
000010  00 7E 87 19 00 00 07 88 19 00 00 0B 88 19 00 00
000020  0F 88 19 00 00 14 88 19 00 00 19 88 19 00 00 1E
000030  88 19 00 00 22 88 19 00 00 27 88 19 00 00 2C 88
000040  19 00 00 31 88 19 00 00 3B 88 19 00 00 40 88 19
000050  00 00 46 88 19 00 00 49 88 19 00 00 4D 88 19 00
000060  00 52 88 19 00 00 55 88 19 00 00 59 88 19 00 00
000070  60 88 19 00 00 66 88 19 00 00 70 88 19
```

This index file is for conference 25.  The values for the offset are as
follows: 84, 88, 92, 127, 135, 139, 143, 148, 153, 158, 162, 167, 172,
177, 187, 192, 198, 201, 205 210, 213, 217, 224, 230, and 240.


[10]: #1--introduction
[11]: #11--intent
[12]: #12--history
[13]: #13--questions-corrections-etc
[20]: #2--conventions--overview
[21]: #21--the-bbs-id
[22]: #22--packet-compression
[23]: #23--packet-transfer--protocols
[24]: #24--specifications--limitations
[30]: #3--qwk-files
[31]: #31--naming-convention
[32]: #32--control-file-control-dat
[33]: #33--welcome-file
[34]: #34--goodbye-file
[35]: #35--news-file
[36]: #36--qmail-deluxe2-menu-file
[37]: #37--new-uploads-listing-newfiles-dat
[38]: #38--bulletin-files-blt-x-y
[39]: #39--message-file-messages-dat
[310]: #310--index-files-ndx
[3101]: #3101--conference-indices
[3102]: #3102--personal-index-personal-ndx
[311]: #311--pointer-file
[312]: #312--session-txt
[40]: #4--rep-files
[41]: #41--naming-convention
[42]: #42--message-file-bbsid-msg
[43]: #43--door-control-messages
[431]: #431--door-id-file
[432]: #432--qmail
[433]: #433--markmail
[434]: #434--kmail
[435]: #435--rosemail
[436]: #436--complete-mail-door
[437]: #437--the-mainmail-system
[438]: #438--bgqwk
[439]: #439--ultrabbs
[4310]: #4310--trimail
[4311]: #4311--cam-mail
[4312]: #4312--qso
[4313]: #4313--tgwave
[44]: #44--turning-off-the-echo-flag
[45]: #45--tag-lines
[50]: #5--net-mail
[a0]: #a--credits-and-contributions
[b0]: #b--sample-turbo-pascal-and-c-code
[c0]: #c--sample-message
[d0]: #d--sample-index-file
