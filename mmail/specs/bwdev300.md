The Blue Wave Offline Mail System Developer's Kit
=================================================

Copyright (C) 1992-1995 by Cutting Edge Computing  
All Rights Reserved

File Structures - Version 3  
November 30, 1995

Created by George Hatchew  
Documentation by Martin Pollard

Markdown by [William McBrine] on 21 January 2022


TABLE OF CONTENTS
-----------------

- [COPYRIGHT AND RESTRICTIONS][cp]
- [TRADEMARK NOTICES][tr]
- [Chapter 1:  INTRODUCTION AND OVERVIEW][ch1]
    - [1.1  Introduction][s11]
    - [1.2  Overview of The Developer's Kit][s12]
- [Chapter 2:  A SHORT HISTORY OF OFFLINE MAIL][ch2]
    - [2.1  Necessity is a Mother...][s21]
    - [2.2  The Pioneers of Offline Mail][s22]
    - [2.3  Open Standards and the Blue Wave Format][s23]
- [Chapter 3:  DESCRIPTION OF BLUE WAVE PACKETS][ch3]
    - [3.1  Filename Conventions][s31]
    - [3.2  The Packet ID][s32]
    - [3.3  The Blue Wave Mail Packet][s33]
    - [3.4  The Blue Wave Reply Packet][s34]
- [Chapter 4:  FILE STRUCTURE TECHNICAL INFORMATION][ch4]
    - [4.1  Data Types][s41]
    - [4.2  Using the File Structures][s42]
    - [4.3  Using the Structure Length Fields][s43]
    - [4.4  Unused and Reserved Fields][s44]
- [Chapter 5:  BLUE WAVE FORMAT DETAILED ANALYSIS][ch5]
    - [5.1  The *.INF File][s51]
    - [5.2  The *.MIX File][s52]
    - [5.3  The *.FTI File][s53]
    - [5.4  The *.DAT File][s54]
    - [5.5  The *.UPL File][s55]
    - [5.6  Reply Message Text][s56]
    - [5.7  The *.REQ File][s57]
    - [5.8  The *.OLC File][s58]
- [APPENDIX A:  OBSOLETE BLUE WAVE STRUCTURES][apa]
    - [A.1  The *.UPI File][aa1]
    - [A.2  The *.NET File][aa2]
    - [A.3  The *.PDQ File][aa3]
- [APPENDIX B:  THE MICROSOFT WINDOWS INI FORMAT][apb]


COPYRIGHT AND RESTRICTIONS
--------------------------

The Blue Wave Offline Mail System File Structures (hereafter known as
"structures" or "the structures") were created by George Hatchew, and
are the copyrighted property of George Hatchew and Cutting Edge
Computing.  Permission is granted for third parties to use these
structures in their own programs, without any royalties or licenses
required.  Cutting Edge Computing reserves the right to make any changes
to these structures, at any time. As such, third parties are requested
not to make any unauthorized changes to these structures, as Cutting
Edge Computing is not bound to follow these changes.  Any proposed
changes should be brought to the attention of Cutting Edge Computing,
where they may be included in future revisions of the structures.

Authors that use these structures are allowed to claim that their
programs are "Blue Wave compatible", provided that such programs can
process mail and reply packets that can be handled without problems or
difficulties by The Blue Wave Offline Mail Doors and Readers from
Cutting Edge Computing.  (Think of it as the "litmus test" for Blue Wave
compatibility.)

Finally, "Blue Wave" is a trademarked term of Cutting Edge Computing,
and cannot be used by authors in the titles of their applications.  This
does not, however, restrict the ability to describe the application as a
"Blue Wave compatible" offline mail application (i.e.  "FooBar: The Blue
Wave-Compatible Offline Mail Door for Widget BBS").


TRADEMARK NOTICES
-----------------

The following are products, trademarks, or registered trademarks of the
following individuals and/or companies:

+ Blue Wave - George Hatchew and Cutting Edge Computing
+ FidoNet - Tom Jennings and Fido Software
+ Macintosh, MacOS - Apple Computer Corp.
+ MS-DOS, Windows, Windows NT, Windows 95 - Microsoft Corp.
+ OS/2, PC-DOS - International Business Machines Corp.
+ PCBoard - Clark Development Inc.
+ PKZIP - PKWARE Inc.
+ QWK, Qmail, DeLuxe2, 1st Reader - Mark "Sparky" Herring
+ Silver Xpress - Hector Santos and Santronics Software
+ Turbo Pascal, Borland Pascal - Borland International
+ XRS - Michael Y. Ratledge

Any omissions from this list are purely unintentional.


Chapter 1:  INTRODUCTION AND OVERVIEW
-------------------------------------

### Section 1.1  Introduction

The Blue Wave Offline Mail System Developer's Kit allows you to create
applications that can process mail and reply packets that are compatible
with The Blue Wave Offline Mail System.  Examples of such applications
include (but are not limited to):

* Mail readers that can display messages within a Blue Wave mail packet,
and allow users to create messages which are then stored in a Blue Wave
reply packet.

* Mail doors, designed for online users of bulletin board systems
(BBSes), that can create Blue Wave mail packets for users to download,
as well as process Blue Wave reply packets that are uploaded by users.

* Utilities that can process Blue Wave mail and reply packets in various
ways.  Examples include utilities to merge mail packets together,
convert mail packets from one format to another (i.e. QWK to Blue Wave
or Blue Wave to QWK), and extract messages from mail packets into ASCII
text files.

The Blue Wave packet format was designed as a means to provide advanced
offline mail capabilities in an easy-to-develop-for design.  Initially
created for the mail technology of the FidoNet network, it has been
expanded to allow offline mail capabilities for the global Internet
network, and has plenty of room to expand for the future.


### Section 1.2  Overview of The Developer's Kit

The following files are included in The Blue Wave Offline Mail System
Developer's Kit:

#### [BLUEWAVE.H]

Blue Wave file structures for C.  To use in your applications, simply
place:

`#include "bluewave.h"`

at the start of your program.  This header uses data types and
preprocessor directives defined in the 1989 ANSI/ISO C standard, and
should be compatible with any C compiler that conforms to the ANSI/ISO
specification. In addition, the data types have been defined in such a
way that the header can be used in both 16-bit and 32- bit programs.

#### [BLUEWAVE.INC]

Blue Wave file structures for Turbo Pascal.  To use in your
applications, simply place:

`{$I bluewave.inc}`

at the start of your program.  This header uses data types and other
language features found only in Borland Turbo Pascal; users of other
Pascal compilers may need to make modifications.  It has been tested
with both Turbo Pascal and Borland Pascal, versions 5.5 through 7.0.

Note that the Turbo Pascal header is provided only as a convenience for
Pascal programmers.  Since all Cutting Edge Computing products are
written in C, little to no support for Pascal programmers can be
provided.  For all intents and purposes, Pascal programmers are on their
own.

The Blue Wave Offline Mail System Developer's Kit is designed for the
professional programmer, one who's been "around the block a few times"
and knows his way around a compiler and the operating system.  (We do
NOT recommend this developer's kit for the less experienced programmer.)
As such, this document was written with the professional programmer in
mind.  It is NOT a tutorial on programming, compilers, or operating
systems; that would be far beyond the scope of this developer's kit.


Chapter 2:  A SHORT HISTORY OF OFFLINE MAIL
-------------------------------------------

Before diving into the specifics of the Blue Wave format, it may be of
interest to examine the history of offline mail, and the role Blue Wave
plays in this little drama.  This will give you an understanding of
where Blue Wave came from, and why it was created.


### Section 2.1  Necessity is a Mother...

The concept of offline mail was popularized in the late 1980s, due
mainly to the ever-increasing popularity of networks such as FidoNet and
RIME.  As the flow of mail increased, systems became increasingly bogged
down as users spent more and more time reading mail online.  This
presented problems for both users and SysOps: Users often racked up
large phone bills due to the enormous amount of time they spent online,
and SysOps became frustrated at the fact that fewer and fewer users
could access their systems (since users were spending more and more time
online).  There HAD to be a better way.

Fortunately, a "better mousetrap" would soon become available: Software
that allowed users to download mail bundles, or "packets", and read them
offline, on their own systems.  Replies could then be created, stored in
reply packets, and uploaded to the BBS the next time the user was
online.  This combination of BBS (door) and user (reader) software
became known as "offline mail systems", and was a godsend all around:
Users got a break in their phone bills, and BBSes became less clogged
with people reading mail online, allowing more users to enjoy them.


### Section 2.2  The Pioneers of Offline Mail

Three gentlemen from the FidoNet and RIME network communities -- Mike
Ratledge, Hector Santos, and Mark "Sparky" Herring -- are generally
regarded as being the pioneers of offline mail.  They were the first to
bring the concept to the masses.

1. Mike Ratledge and XRS.  XRS, or Express Response System, was an
offline mail door/reader combination for the QuickBBS bulletin board
system.  XRS became quite popular within the QuickBBS community, but
unfortunately, never developed beyond it.  Updates to XRS soon became
few and far between, and eventually, Mr. Ratledge officially ended
development of XRS.

2. Hector Santos and Silver Xpress.  Like XRS, the Silver Xpress
door/reader combination was originally written for a specific BBS
package, (Opus; in fact, Silver Xpress was originally called Opus
Xpress).  Unlike XRS, however, the Silver Xpress software line branched
out into other BBS packages, and has become quite popular with users.
Software developers, however, do not enjoy that popularity, as the
advanced Silver Xpress packet format is proprietary.  As such, no
third-party doors, readers, or utilities can be created that will work
with Silver Xpress mail packets, and limits Silver Xpress to only those
software and hardware platforms that are supported by Santronics
Software.

3. Mark "Sparky" Herring and QWK.  The QWK mail format was developed out
of the message base format used by the PCBoard BBS software, and was
used by Mr. Herring to create a door (Qmail) and reader (DeLuxe2, then
1st Reader) for it. Eventually, the format of QWK mail packets became
widely known, and many QWK-compatible doors and readers sprang into
existence, making QWK one of the most popular formats available for
offline mail.  Unfortunately, there is a down side to QWK, which will be
explained shortly.


### Section 2.3  Open Standards and the Blue Wave Format

The availability of an open standard for offline mail has several
advantages, the main one being that anyone can create a door, reader, or
utility which processes mail packets in such a format. It's not
restricted to one person or platform.

The QWK format is one such format; to date, there are dozens of doors,
readers, and utilities that support QWK.  Unfortunately, the format has
one rather glaring drawback, in that there is no consistency to it.
Mark Herring never officially released the specifications for the QWK
format to the public; by all reports, it was "hacked out" by several
people.  As such, much of what is supposed to be done to create a QWK
mail packet is left open to interpretation.  Also, QWK was never
designed to handle the complexities of mail from such networks as
FidoNet and the Internet (as mentioned earlier, QWK was derived from the
PCBoard message base format, and PCBoard did not support FidoNet and
Internet mail until very recently).  Because of this, authors of QWK
products took it upon themselves to add their own enhancements and
extensions to the format, most of which conflict with each other.  The
result is that the "QWK standard" is hardly a standard at all, and
anyone who wishes to write a program to fully handle QWK packets must go
through lots of programming gymnastics to support all the variations.

Enter the Blue Wave format.  The Blue Wave Offline Mail System was
developed by George Hatchew in 1990, after becoming dissatisfied with
the offline mail system offerings of the time.

For several years, the Blue Wave format was as proprietary as the XRS
and Silver Xpress formats, until Mr. Hatchew decided to add QWK packet
support to his reader.  He then became painfully aware of the many
problems inherent in the QWK format, and decided:

1. That a more powerful format needed to be made available to the
public, one that would handle the many complexities of current mail
systems, while at the same time be easy for developers to implement.

2. That control over future enhancements and development of the format
be retained by a single party, in order to avoid the same chaos that is
rampant in the QWK developer community.

Thus, Mr.  Hatchew decided to make the newly-overhauled Blue Wave format
available to third-party developers, subject to the condition that any
enhancements or modifications be approved and made ONLY by him (to
prevent the Blue Wave format from receiving the same "hatchet job" that
the QWK format has undergone).  Since that time, over two dozen Blue
Wave compatible doors, readers, and utilities have been created,
covering a wide range of hardware (IBM PC, Macintosh, Amiga) and
operating systems (MS- DOS, Microsoft Windows, OS/2, MacOS), with more
applications arriving all the time.  In addition, several BBS programs
are now including Blue Wave offline mail as an integral part of the BBS
software, with more sure to follow.


Chapter 3:  DESCRIPTION OF BLUE WAVE PACKETS
--------------------------------------------

Any discussion of the Blue Wave format must begin with a description of
the concepts of "mail packets" and "reply packets", as well as the
individual files found in both.  Such information is essential to
understanding the Blue Wave format as a whole.


### Section 3.1  Filename Conventions

The Blue Wave Offline Mail System was originally developed for IBM PC
and compatible computer systems running MS-DOS (or a compatible
operating system such as PC-DOS or Novell DOS).  As such, the Blue Wave
format revolves around the DOS filename convention: One to eight
characters, followed by an optional period and up to three additional
characters (the "DOS filename" or "8.3 filename" format).

Other operating systems, such as OS/2, Windows NT, and Windows 95, allow
filenames that can be up to 255 characters long, and do not have to
conform to the DOS filename convention.  However, in order to keep mail
and reply packets compatible with DOS systems -- still the most widely
available, and thus the "lowest common denominator" -- the Blue Wave
format requires that the DOS filename convention be used, even on
systems that don't have this limitation.

Finally, DOS filenames can consist of letters, numbers, and several
punctuation characters.  However, if you plan to write a Blue Wave
compatible application that will create packets which will be used on
non-DOS systems, you should keep in mind the fact that some systems
might not be able to handle any characters other than letters and
numbers.  (This is especially true for ASCII characters 128 through 255,
which DOS will allow but other systems almost certainly will not.  You
should therefore consider these characters to be strictly off limits.)


### Section 3.2  The Packet ID

Blue Wave mail and reply packets revolve around what is called the
"packet ID".  This is a string of one to eight characters, and serves as
a unique identifier for each host system.  It is mainly used to create
the filenames for mail and reply packets, and the data files contained
within each.  (For example, the packet ID for The Wild! Blue BBS is
"WILDBLUE".)


### Section 3.3  The Blue Wave Mail Packet

A Blue Wave mail packet actually consists of several files, all bundled
together into an archive file.  (It is assumed that you are already
familiar with archiving utilities, such as PKWare's PKZIP and PKUNZIP.)
The mail packet is created on a host system, and contains messages
selected by the user to be read offline.

Mail packets consist of the following data files ("`<packetid>`" refers
to the packet ID used by the host system):

#### `<packetid>.INF`

Contains information about the host system, the user who requested the
mail packet, and the message areas available on the host system.

#### `<packetid>.FTI`

Contains the header information for each message obtained from the host
system.  The information contained in the headers include the person who
wrote the message (the "From" field), the person to whom the message was
addressed (the "To:" field), and so on.

#### `<packetid>.MIX`

An index file designed to provide quick access to messages within the
mail packet.

#### `<packetid>.DAT`

The text of each message obtained from the host system.

Optionally, some additional files may also be present in the mail
packet.  These files include:

Text files containing bulletins and other announcements from the host
system.  They can either be specified in the \*.INF file, or be present
as files named "BLT*.*" (any filename is valid, so long as it begins
with "BLT") or "*.TXT" (i.e. any filename with an extension of .TXT).
The methods used to display these bulletins is up to the programmer.

A file called "NEWFILES.*" (any extension is valid), a text file listing
all new files available for download from the host system.  The methods
used to display this list is up to the programmer.

The filename to be used for mail packets consists of the packet ID,
followed by an extension in one of two formats:

DAY OF WEEK - Comprised of the first two characters of the weekday name,
followed by a digit from 0 to 9.  Examples: SU1, MO4, TH0, FR9.  Keeping
track of the next valid digit to be used for a user's mail packet is up
to the programmer.

NUMERIC - Comprised of a one to three digit number, i.e.  0-9, 00-99, or
000-999. (Using a three digit number is highly recommended.)  Keeping
track of the next valid number to be used for a user's mail packet is up
to the programmer.

Some examples of mail packet filenames are WILDBLUE.SU0 and
WILDBLUE.019.


### Section 3.4  The Blue Wave Reply Packet

A Blue Wave reply packet actually consists of several files, all bundled
together into an archive file.  It is created by the offline reader, and
contains messages written by the user that are to be uploaded to a host
system.

Reply packets consist of the following data files ("`<packetid>`" refers
to the packet ID used by the host system):

#### `<packetid>.UPL`

Contains information about the reader, as well as the header information
and the filename containing the message text (see below) for each
message in the reply packet.

#### `<filename>`

A text file containing the text of a reply message. Each reply message
is stored in its own individual text file, and each text file can only
correspond to ONE reply message (that is, one text file cannot be used
for multiple reply messages).  The names used for these files is up to
the programmer.

In addition, one of more of the following optional files may be
contained in the reply packet:

#### `<packetid>.REQ`

Contains a list of files that the user wishes to download from the host
system.  The procedure used to actually download requested files is left
to the programmer.

#### `<packetid>.OLC`

Contains information used to configure the offline mail features on the
host system.  Also known as "offline configuration" or "door
configuration".  The *.OLC file is a plain text file, making it easier
to add enhancements and extensions.

The following files are now officially obsolete, but door authors may
still need to write code to handle them:

#### `<packetid>.UPI`

Contains information about the reader, as well as the header information
and the filename containing the message text for each NON-NETMAIL
message in the reply packet.

#### `<packetid>.NET`

Contains the header information and the filename containing the message
text for each NetMail message in the reply packet.

#### `<packetid>.PDQ`

Contains information used to configure the offline mail features on the
host system.

The functions of the *.UPI and *.NET files were combined into the *.UPL
file.  The limited *.PDQ file was replaced by the more extensible *.OLC
file (the latter being a plain text file, and hence easier to add
updates and new features).

The filename of the reply packet consists of the packet ID and the
extension ".NEW".  For example, a reply packet destined for The Wild!
Blue BBS would be assigned the name "WILDBLUE.NEW".


Chapter 4:  FILE STRUCTURE TECHNICAL INFORMATION
------------------------------------------------

This chapter goes into the technical details behind the Blue Wave file
structures.  Such details include the data types that are used and how
they're stored in the structure, how the structures are defined in
programs, "big endian" versus "little endian" CPU architectures, and
reserved fields in the structures.


### Section 4.1  Data Types

The following data types are used in the structure definitions:

#### `tBYTE`

An unsigned 8-bit integer.  Allowable values range from 0 to 255.

#### `tCHAR`

An unsigned 8-bit integer.  Allowable values range from 0 to 255.
Used to define an array of ASCII characters, aka a "string". (Strings
are stored in C fashion: Zero or more characters terminated with an
ASCII 0 character.)

#### `tINT`

A signed 16-bit integer.  Allowable values range from -32,768 to
32,767.

#### `tWORD`

An unsigned 16-bit integer.  Allowable values range from 0 to
65,535.

#### `tLONG`

A signed 32-bit integer.  Allowable values range from
-2,147,483,648 to 2,147,483,647.

#### `tDWORD`

An unsigned 32-bit integer.  Allowable values range from 0 to
4,294,967,295.

Since the Blue Wave format was originally designed for the IBM Personal
Computer and compatible systems, multi-byte data types (`tINT`, `tWORD`,
`tLONG`, and `tDWORD`) are expected to be stored in the format used by the
Intel 80x86 CPU.  This format is called "little endian", and specifies
that the least significant byte is to be stored first, followed by the
next significant byte, and so on.  However, some CPUs -- such as the
Motorola 68000 -- store multi-byte data types in the exact opposite way
(known as "big endian" format).  Using the Blue Wave structures on a
"big endian" CPU will be discussed in a moment.


### Section 4.2  Using the File Structures

As explained in [section 1.2][s12], the file structures are provided as
a header file for use with the C programming language.  To include the
header file into your program, simply place the statement:

`#include "bluewave.h"`

at the start of your source file.

Each file structure is defined as a C data structure (`struct`) via the
`typedef` directive.  This makes it easy to define variables and
pointers in your programs.  For example:

`INF_HEADER infhdr;`

defines the `infhdr` variable as a data structure of type `INF_HEADER`.
(Again, it is assumed that you are familiar with the C language and the
concepts of structures, type definitions, and so forth.)

Accessing fields in the data structures is accomplished just like any
other C variable or data structure.  However, if you are programming for
a "big endian" CPU, you have an additional chore: Converting data types
between "little endian" and "big endian". First off, place the following
line in your program BEFORE you include the header file, like so:

```
#define BIG_ENDIAN
#include "bluewave.h"
```

This will define the 16-bit and 32-bit data types as arrays of bytes.
You must then write functions that will convert these fields from
"little endian" to "big endian" and back again, and use these functions
whenever you access a structure field.

Finally, all structures must be stored in "packed" format; that is, the
compiler must not insert padding between the data fields in order to
force those fields onto CPU word boundaries.  Most Intel compilers
default to "packed" mode, but if yours does not, you must use the
appropriate compiler commands or preprocessor directives to set "packed"
mode before including the header file in your program.  If this is not
done, mail and reply packets generated by your program will not be
compatible with the vast majority of Blue Wave compatible programs.


### Section 4.3  Using the Structure Length Fields

Several of the file structures include fields that define the lengths of
the other structures used in mail and reply packets. These fields are
used to ensure that programs can use future versions of the structures
without breaking.

Authors should take the time to add the few extra lines of code needed
to fill in the structure length fields, and take into account (when
reading mail and reply packets) the possibility of extensions to the
file structures.  If the structures are LONGER than expected, simply
perform a seek() to move past the additional information to the next
record.  If the structures are SHORTER than expected -- a situation
which you should almost never encounter, but is nevertheless possible --
a simple "please upgrade your door [or reader]" should suffice, as any
additional information that is sent in the structure probably would not
be crucial, and you will most likely be able to continue without it.

(It should be noted that all versions of The Blue Wave Offline Mail Door
prior to version 3.00 set these fields to 0, as this extensibility was
not added to the Blue Wave format until the 3.00 series was released.
If the structure length fields are 0, applications should assume that
all records are of the sizes defined as the "ORIGINAL_xxx_LEN" macros.
Note that there is no definition for the original length of the *.UPL
structures, as the pre-3.00 doors did not recognize *.UPL files.)

An example of how to use these structure length fields is given in the
comments at the start of the header file.  A snippet of C code not only
demonstrates the length fields, but the use of the ORIGINAL_xxx_LEN
macros as well.


### Section 4.4  Unused and Reserved Fields

There are plenty of fields in the Blue Wave structures that are not yet
being used, and are marked as "reserved" in the header file.  THESE
AREAS ARE NOT TO BE USED BY PROGRAMMERS.  They are reserved for future
expansion of the Blue Wave format, and if you use them for your own
purposes, you run the very real risk of making your program incompatible
with future versions of the Blue Wave format.

Future updates of the Blue Wave structures will assume that any unused
fields (including unused bits in bit-flag fields) are "garbage free"
(i.e. filled with binary zeroes).  To accomplish this, you should use
the memset() function to zero out the structure before adding
information to it.  For example, using:

`memset(&inf_record, 0, sizeof(INF_AREA_INFO));`

prior to putting information into the structure will ensure that
programs using future versions of the structures will not break on
packets created by programs using older structures.

Again, WE CANNOT STRESS HIGHLY ENOUGH THE IMPORTANCE OF ENSURING THAT
UNUSED FIELDS ARE CLEARED TO BINARY ZERO!  Get into the habit of zeroing
out structures before adding information to them and writing them to
disk; a little work now will save everyone a whole lot of grief in the
long run.


Chapter 5:  BLUE WAVE FORMAT DETAILED ANALYSIS
----------------------------------------------

This chapter goes into detail on each file in the Blue Wave mail and
reply packets; the file structures that comprise each file; and the
fields in each file structure.


### Section 5.1  The *.INF File

The *.INF file consists of two parts: A single required header, and zero
or more message area information records corresponding to each area on
the host system to which the user has access.

Note that ALL message areas to which the user has access MUST be
included in the *.INF file.  The only time an area can be omitted is if
the user does not have access to that area on the host system.  This is
necessary in order for the user to be able to add and/or drop message
areas via offline configuration.

The header is called `INF_HEADER`, and contains the following fields:

#### `ver`

The version of the Blue Wave format being used.  This value can be
obtained using the `PACKET_LEVEL` macro defined in the header file.

#### `readerfiles[]`

An array specifying the filenames of the bulletin files included in the
packet by the host system.  The method used to display these files is up
to the programmer. Up to 5 files can be specified.

#### `regnum`

Used by The Blue Wave Offline Mail Door to store the registration code
of the door.  Third-party authors should ignore this field.

#### `mashtype`

Used by The Blue Wave Offline Mail Reader to store the compression type
of the mail packet archive.

#### `loginname`

The name used by the user when logging on to the host system.  This is
usually the user's real name.  Doors are required to fill this field.

#### `aliasname`

An alternate name used by the user on the host system. This is usually
the user's alias, or "handle".  If this field is empty, it usually means
that the host system does not support alternate names, and the loginname
field should be used instead.

#### `password`

The password required to access the mail packet.  As a simple security
measure, each character of the password is stored as the ASCII value
plus 10.

#### `passtype`

A value indicating the type of password specified in the password field.
0 indicates no password, 1 indicates a door password, 2 indicates a
reader password, and 3 indicates both a door and a reader password.

#### `zone, net, node, point`

These four fields make up the main FidoNet-style network address of the
host system.  If the host system does not have a FidoNet-style address,
these fields should be set to 0.

#### `sysop`

The name of the SysOp of the host system.

#### `ctrl_flags`

Bit-mapped flags that control certain features of the reader.  Refer to
the header file for a list of available flags.

#### `systemname`

The name of the host system.

#### `maxfreqs`

The maximum number of files that the user may request from the host
system.  (This field is intended for use with the reader's offline file
request feature.)

#### `is_QWK`

Normally, the Blue Wave reader stores a copy of the *.INF file each time
a mail packet is open, so that it can process reply packets without the
need to open a mail packet.  QWK mail packets, however, do not have
*.INF files, thus the Blue Wave reader creates one and sets this flag.
This way, the reader will properly access the *.REP reply packet
(instead of trying to access a Blue Wave format *.NEW reply packet).

#### `uflags`

Bit-mapped flags that indicate the options enabled by the user on the
host system mail door.  Refer to the header file for a list of available
flags.  (This field is intended for use with the reader's offline
configuration feature.)

#### `keywords[]`

An array specifying the mail bundling keywords defined by the user on
the host system mail door.  Up to 10 keywords may be specified.  (This
field is intended for use with the reader's offline configuration
feature.)

#### `filters[]`

An array specifying the mail bundling filters defined by the user on the
host system mail door.  Up to 10 filters may be specified.  (This field
is intended for use with the reader's offline configuration feature.)

#### `macros[]`

An array specifying the mail bundling macros defined by the user on the
host system mail door.  Up to 3 macros may be specified.  (This field is
intended for use with the reader's offline configuration feature.)

#### `netmail_flags`

Bit-mapped flags indicating the NetMail status flags that the user is
allowed to specify on NetMail messages sent through the host system.
Refer to the header file for a list of available flags.

#### `credits`

The current amount of NetMail credit accumulated by the user on the host
system.

#### `debits`

The current amount of NetMail debit accumulated by the user on the host
system.

#### `can_forward`

A flag that indicates if the host system allows the user to forward
messages.  A non-zero value indicates that forwarding is allowed.

#### `inf_header_len`

Indicates the size of the `INF_HEADER` structure.

#### `inf_areainfo_len`

Indicates the size of the `INF_AREA_INFO` structure.

#### `mix_structlen`

Indicates the size of the `MIX_REC` structure.

#### `fti_structlen`

Indicates the size of the `FTI_REC` structure.

#### `uses_upl_file`

A flag that indicates if the host system can handle a *.UPL file in the
reply packet.  A non-zero value indicates that *.UPL files are
supported.

Note that version 3 and later of the Blue Wave format requires the use
and support of *.UPL files.

#### `from_to_len`

The maximum length of the From: and To: fields that the host system can
support.  If this value is 0, then a value of 35 must be assumed (35
characters is the limit in the Blue Wave reply header).

#### `subject_len`

The maximum length of the Subject: field that the host system can
support.  If this value is 0, then a value of 71 must be assumed (71
characters is the limit in the Blue Wave reply header).

#### `packet_id`

The host system packet ID.  This is provided so that even if the mail or
reply packets are renamed to something completely different, doors and
readers will still be able to work with the proper files (the files
inside the mail and reply packets use the packet ID as part of the
filename).

Note that this field was not supported until version 3.00 of The Blue
Wave Offline Mail Door.  If this field is empty, reader authors should
assume that the root name of the *.INF filename is the packet ID.

#### `file_list_type`

Indicates the type of new file list that is generated by the host
system.  Refer to the header file for a list of valid types.  (This
field is intended for use with the reader's offline configuration
feature.)

#### `auto_macro[]`

An array of flags specifying which of the macros in the `macros[]` array
are auto-macros (i.e. the door executes them automatically every time a
user requests a mail packet be bundled for download).  Up to three flags
may be specified, each corresponding to the three entries in the
`macros[]` array.  A non-zero value indicates that a particular macro is
an auto-macro.  (This field is intended for use with the reader's
offline configuration feature.)

#### `max_packet_size`

Specifies the maximum size (in kilobytes) allowed for an uncompressed
mail packet.  A zero value specifies that there is no limit to the
maximum size of the mail packet.  (This field is intended for use with
the reader's offline configuration feature.)


The message area information record is called `INF_AREA_INFO`, and
contains the following fields:

#### `areanum`

The area number on the host system.  The string placed in this field may
(and usually will) correspond to a numeric value; some systems may allow
non-numeric characters as an area identifier, which is perfectly legal.
Duplicate area numbers are not allowed.

Doors must place the value contained here into the *.MIX index file
record (discussed later).  Readers normally won't need to use this
field, except perhaps when displaying the list of message areas to the
user.

#### `echotag`

A tag name that uniquely identifies the message area. Duplicate tag
names are not allowed.  (For FidoNet EchoMail areas, we recommend using
the EchoMail tag name, as they are also required to be unique.)

Readers will use this field when generating records in the *.UPL reply
file (discussed later).  Doors can then use the value to ensure that
messages are posted to the proper message areas (if, for example, the
SysOp has added, deleted, or moved message areas around on the host
system).

#### `title`

A short description of the message area.  Most host systems will provide
this information; if not, you can probably use the value in the
"echotag" field.

#### `area_flags`

Bit-mapped flags indicating the status and usage of the message area.
Refer to the header file for a list of available flags.

#### `network_type`

Indicates the type of network to which messages in this area belong.
Refer to the header file for a list of available network types.
(Currently, the Blue Wave format supports the FidoNet and Internet
networks.)


### Section 5.2  The *.MIX File

The *.MIX file consists of zero or more records, each corresponding to a
message area listed in the *.INF file.  Each *.MIX record contains
information on the number of messages in each area, as well as the
offset in the *.FTI file (described later) to the start of the message
headers for each area.

The index record is called `MIX_REC`, and contains the following fields:

#### `areanum`

The area number on the host system.  This field must contain the same
value as the corresponding field in the *.INF record.

#### `totmsgs`

The total number of messages in the area.  (Note that this is the number
of messages that were downloaded from the host system.)

#### `numpers`

The total number of personal messages (i.e. messages addressed to the
user) in the area.

#### `msghptr`

The offset (in bytes) in the *.FTI file (described later) to the start
of message headers for the area. This allows readers to quickly find the
message headers for the area, rather than having to scan through the
entire *.FTI file for them.  (If there are no messages in the area, this
field is irrelevant, and should be ignored by readers.)


### Section 5.3  The *.FTI File

The *.FTI file contains the header information for each message
downloaded from the host system.  This includes the names of the persons
who wrote the messages and who they are addressed to, the date and time
each message was written, and the offsets into the *.DAT file (discussed
later) for the text of each message.

Note that message headers for each area must be in area number order,
and all messages for an area must be grouped together. That is, all
headers for the first message area will appear in the *.FTI file first,
followed by the headers for the second area, then the third area, and so
on.

The message header record is called `FTI_REC`, and contains the following
fields:

#### `from`

The name of the person who wrote the message.

For Internet E-mail messages and Usenet newsgroup articles, this field
should contain the name derived from the "From:" line in the RFC header,
which is usually in one of the following formats:

    From: George Hatchew <bwave@ibm.net>
    From: bwave@ibm.net (George Hatchew)

and may or may not be contained within quote marks.  If the name cannot
be accurately determined, the Internet address can be used instead.
(Refer to section 5.4, "The *.DAT File", for information on preserving
the RFC header for Internet messages.)

#### `to`

The name of the person to whom the message is addressed.

For Usenet newsgroup articles, the text "All" should be placed in this
field, as Usenet articles are not addressed to specific individuals.
For Internet E-mail messages, this field should contain the name derived
from the "To:" line in the RFC header, which is usually in one of the
following formats:

    To: George Hatchew <bluewave@cris.com>
    To: bluewave@cris.com (George Hatchew)

and may or may not be contained within quote marks.  If the name cannot
be accurately determined, the Internet address can be used instead.
(Refer to section 5.4, "The *.DAT File", for information on preserving
the RFC header for Internet messages.)

#### `subject`

The subject of the message.  For Internet E-mail messages and Usenet
newsgroup articles, this field should contain as much of the text in the
"Subject:" line of the RFC header as possible.  (Refer to section 5.4,
"The *.DAT File", for more information on preserving the RFC header for
Internet messages.)

#### `date`

The date and time the message was written.  There is no exact format
required for this field; it is mainly used for display purposes in the
reader.  We recommend the use of the Fido date format ("DD MMM YY
HH:MM:SS"), if possible.

#### `msgnum`

The number corresponding to the message stored on the host system.
Values greater than 65,535 should be wrapped (i.e.  65,536 will be 0,
65,537 will be 1, and so on).  This field is used mainly for display
purposes in the reader.

#### `replyto`

The message number on the host system to which this message is a reply.
Used in the reader to jump back and forth along a message thread.

#### `replyat`

The message number on the host system which is a reply to this message.
Used in the reader to jump back and forth along a message thread.

#### `msgptr`

The offset (in bytes) to the start of the text of this message in the
*.DAT file (described later).  Each message in the mail packet MUST have
corresponding text in the *.DAT file; refer to section 5.4, "The *.DAT
File", for more information.

#### `msglength`

Length of the message text, in bytes.

#### `flags`

Bit-mapped flags indicating the status of the message. Refer to the
header file for a list of available flags.

#### `orig_zone, orig_net, orig_node`

For FidoNet NetMail messages, these fields indicate the address of the
node (zone:net/node) that created the message.


### Section 5.4  The *.DAT File

The *.DAT file contains the text of each message in the mail packet.  A
message consists of a mandatory space character (ASCII 32), followed by
zero or more characters.  (ASCII characters 1 to 255 are valid.  ASCII 0
is not permitted, since it is used as a string terminator in the C
language.)  The message does not need to be terminated with an ASCII 0
character, since the exact length of the text (including the leading
space character) is stored in the *.FTI record.

The formatting of text follows the standard established by FidoNet, with
each paragraph terminated by a carriage return (ASCII 13).  Linefeeds
(ASCII 10), if present, are to be ignored. So-called "soft carriage
returns" (ASCII 141), which some editors use for formatting lines of
text, should generally be ignored except under special circumstances
(such as in situations where foreign or double-byte character sets are
recognized).  "Hidden text lines" are allowed by placing an ASCII 1
character at the start of the line, and terminating the line with a
carriage return; such lines should generally not exceed 79 characters.
In FidoNet bases, hidden text lines serve as extensions to the specs of
FidoNet messages, and are used to specify control information that is
otherwise incomplete or unavailable.  (FidoNet refers to these lines as
"kludge lines" or "control lines".)

Note that if the host system uses a different character to terminate
lines and paragraphs, that character should be replaced with a carriage
return when packing messages.  An example of such a system is PCBoard,
which uses an ASCII 227 for terminating lines and paragraphs in
messages.

#### General guidelines for packing messages in doors:

##### Local message bases

The entire text of the message can be packed verbatim.

##### FidoNet NetMail bases

Again, the text of the message can be packed verbatim. You may also
elect to strip out kludge lines, as they add unnecessary overhead to the
mail packet (this is usually provided as a user-selectable option).

Note that the `FMPT` kludge line, which specifies the point number portion
of the origin address, MUST be present in NetMail messages if the point
number is not zero.  This is to overcome an oversight in the Blue Wave
specifications (the *.FTI record does not include a field for the point
number).  If a message contains this line, it must not be stripped out;
if it does not have one, but the point number is not zero, then it must
be added to the message during packing.  The format is `FMPT nnn`, where
"nnn" is the point number (the line is preceded by an ASCII 1, and
terminated with a carriage return).

##### FidoNet EchoMail bases

Once again, the text can be packed verbatim, and hidden text lines may
be stripped out at the user's request.

Note that the `MSGID` kludge line, if present, MUST NOT be stripped, in
order to accommodate reply linking. (Reply linking is discussed in
section 5.5, "The *.UPL File".)  Also note that `SEEN-BY` lines, which
indicate the systems that have seen the message, can be treated as
kludge lines; they can be stripped at the user's request, or they can be
included in the text (and should, ideally, be hidden behind ASCII 1
characters, so that they will be treated by readers as hidden text
lines).

##### Internet and Usenet bases

Internet E-mail and Usenet newsgroup messages, as obtained from the
Internet, follow the format specified in Internet document [RFC-822].
This format calls for messages to be stored as lines of text, with the
header lines first, followed by a blank line, then the lines of message
text.  Most host systems, when importing Internet E-mail and Usenet
newsgroups, will usually retain this format.

When packing [RFC-822] style messages, the header lines must be converted
to hidden text lines; these hidden text lines must not be stripped.  Not
only will this hide the header from the user (in readers that suppress
the display of hidden text lines), it allows the reader to more easily
find header lines when creating reply messages, as creating replies to
these messages requires information that can only be obtained from the
[RFC-822] header.

#### General guidelines for handling messages in readers:

If the initial space character is not present when reading the message
text, the reader can assume that the message text (and possibly the
enter *.DAT file itself) has been damaged.  The character itself is not
to be displayed, as it is only used as an indicator in the *.DAT file.

If a line of text is wider than the display area, the text should be
wrapped.  Generally, this wrapping is done at the first space character
before the display area limit, so that words do not get chopped in half.

Hidden text lines should ideally be suppressed by the reader, as they
usually contain control information that is not important to the user.
Under such circumstances, the reader should provide an option for
displaying these lines upon request of the user.  (For example, The Blue
Wave Offline Mail Reader will -- starting with version 2.20 -- only
display hidden text lines in a pop-up window when the user enters the
command to do so.)  It's the best of both worlds: The lines are hidden,
yet available to the user whenever the need arises.


### Section 5.5  The *.UPL File

The *.UPL file consists of two parts: A single required header, and zero
or more reply records corresponding to each message contained in the
reply packet.  Each reply record includes the names of the persons who
wrote the replies and who they are addressed to, the date and time the
reply was written, and the name of the file containing the text of the
reply message.

Note that reply records do not have to appear in any particular order,
save for the fact that they must always follow the header.

The header record is called `UPL_HEADER`, and contains the following
fields:

#### `regnum`

Used by The Blue Wave Offline Mail Reader to store the registration code
of the reader.  Third-party authors should ignore this field.

#### `vernum`

The version number of the reader.  As a simple security measure, each
character of the version number is stored as the ASCII value plus 10.

#### `reader_major`

The major version number of the reader (i.e. the number to the left of
the decimal point).  For example, the value 2 would be stored here for
version 2.20.

#### `reader_minor`

The minor version number of the reader (i.e. the number to the right of
the decimal point).  For example, the value 20 (decimal) would be stored
here for version 2.20.

#### `reader_name`

The name of the reader.  Door programmers can use this field to display
the name of the program which created the reply packet.

#### `upl_header_len`

Indicates the size of the `UPL_HEADER` structure.

#### `upl_rec_len`

Indicates the size of the `UPL_REC` structure.

#### `loginname`

The name used by the user when logging on to the host system.  It should
be filled with the name found in the corresponding field in `INF_HEADER`,
and can be used by door authors as a possible security measure.

#### `aliasname`

An alternate name used by the user on the host system. It should be
filled with the name found in the corresponding field in `INF_HEADER`,
and can be used by door authors as a possible security measure.

#### `reader_tear`

The abbreviated name of the reader.  Doors can use this field, in
conjunction with the `vernum` field, to add "brag lines" (and tear lines
in FidoNet EchoMail bases) to reply messages when storing them in the
host's message bases.

Note that older readers, such as versions of The Blue Wave Offline Mail
Reader prior to version 2.20, may not fill in this field.  If it is
blank, the brag/tear line to be generated is left to the discretion of
the author.

#### `compress_type`

Used by The Blue Wave Offline Mail Reader to store the compression type
used by the reply packet.

#### `flags`

Used by The Blue Wave Offline Mail Reader to store various flags
required for reply message processing.

#### `not_registered`

A flag that indicates the reader is not registered.  If it is non-zero,
the reader that generated the reply packet has not been registered by
its user.  Doors can use this field to add an indicator to the brag/tear
line which shows that the reader is not registered (for example, The
Blue Wave Offline Mail Door will append "[NR]" to the line).

The reply message record is called `UPL_REC`, and contains the following
fields:

#### `from`

The name of the person who wrote the reply message. This field should
only be used for message areas that allow any name to be entered in the
From field; otherwise, the user's name or alias (obtained from the host
system) should be used.

#### `to`

The name of the person to whom the reply message is addressed.  Note
that this field applies only to local and FidoNet areas only.  For
Internet E-mail areas, the destination E-mail address is obtained from
the `net_dest` field (described below).  Usenet newsgroups do not use a
To field, thus doors should use the text "All" when storing messages on
host systems that have such a field.

#### `subj`

The subject of the reply message.  For Internet E-mail and Usenet
newsgroup areas, doors should examine the text of the reply message for
a Subject control line, and compare the text in that line with this
field (ignoring any "Re:" sequences).  If the two are equal, up to the
length of the text in this field, the door should assume that the user
did not change the subject, and use the text in the Subject line instead
of the text in this field (in order to preserve subjects longer than 71
characters).  Otherwise, the door can assume that the user has changed
the subject, and should use the text in this field.

#### `destzone, destnet, destnode, destpoint`

For FidoNet NetMail reply messages, these fields indicate the address
(zone:net/node.point) of the node to whom the message is addressed.

#### `msg_attr`

Bit-mapped flags indicating the status of the reply message.  Refer to
the header file for a list of available flags.

#### `netmail_attr`

For FidoNet NetMail reply messages, these bit-mapped flags indicate the
attributes that should be applied to the message.  Refer to the header
file for a list of available flags.

#### `unix_date`

The date and time the message was written, stored Unix style as the
number of seconds since January 1, 1970. Universal Coordinated Time
(UTC) should be taken into account when storing and using this value
(the date and time functions in many C compilers handle this
automatically).

#### `replyto`

The message number (obtained from the `msgnum` field in `FTI_REC`) to 
which this message is a reply.

#### `filename`

The name of the file which contains the text of the reply message.  If
the file does not exist in the reply packet, or if this field is empty,
the door should consider the record to be invalid.

#### `echotag`

The tag for the message area on the host where this reply message should
be stored.  This field must contain the same text as the corresponding
field in `INF_AREA_INFO`, and is used by the door to ensure that the reply
message ends up in the proper area.

#### `area_flags`

Contains the value stored in the corresponding field in `INF_AREA_INFO`,
and is used to ensure that later editing of the reply message is handled
properly.  This is useful in situations where the reader has not kept a
copy of the *.INF file from the host system and the user wishes to
modify the reply packet without also opening a mail packet for the host
system.  Door authors should ignore this field.

#### `f_attach`

The name of the file that is "attached" to the reply message.  Used on
host systems that support attaching files to messages.  If the
`UPL_HAS_FILE` flag in the `msg_attr` field is not set, or the area does
not support attached messages, doors should ignore this field.

#### `user_area`

Provided for reader authors to use as they see fit, to store reader-
specific information for the reply message.  Doors should ignore this
field.

Note that even though this field is provided for your use, the use of
a separate file (one that is not part of the official Blue Wave
specification) is recommended.  The reason for this is that reader
authors have no way of distinguishing the information in this field;
thus, if the reply packet is handled by more than one reader, the
information in this field could be clobbered.

#### `network_type`

Contains the value stored in the corresponding field in `INF_AREA_INFO`,
and is used to indicate the type of network to which the reply message
belongs.  This allows for proper processing of the reply message, as
doors and readers can tell "at a glance" which fields in the reply
record should be used for the particular network type.

#### `net_dest`

For Internet E-mail reply messages, this field contains the E-mail
address of the person to whom the reply message is destined.  It is this
text that will be placed in the To: line of the [RFC-822] header.

For FidoNet NetMail and EchoMail messages, this field is used to store
the information necessary for the door to generate a `MSGID`/`REPLY` kludge
line, as per FidoNet technical document [FTS-0009].  The text to be stored
in this field should be in the format `REPLY: msgid_info`, where
"msgid_info" is the information following the `MSGID` keyword.  Doors can
then take the information in this field and store it in the message on
the host system (be sure to place an ASCII 1 in front of it).

Note that doors should ensure that the field is valid by testing to see
if it begins with `REPLY:` (FidoNet areas only).  If it does not, the
field should be ignored.


### Section 5.6  Reply Message Text

As indicated above, the text of each reply message is stored in its own
separate file in the reply packet.  As with the message text stored in
the *.DAT file, the text stored in a reply message text file consists of
zero or more characters (ASCII values 1 through 255), with each line or
paragraph terminated by a carriage return (ASCII 13).  Linefeeds (ASCII
10) are optional, as are so-called "soft" carriage returns (ASCII 141).
Carriage returns and linefeeds should be handled according to the
conventions of the host system (for example, PCBoard requires that lines
and paragraphs be terminated with an ASCII 227 character; in such cases,
carriage returns should be converted to ASCII 227, and linefeeds should
be ignored).

For local and FidoNet reply messages, readers should not generate any
hidden text lines in the message text.  Doors should strip any such
lines before storing the message text on the host system.  This is to
prevent malicious users from sending out fake control information
through FidoNet.  (Authors should obtain FidoNet technical documents
[FTS-0001], [FTS-0004], and [FTS-0009] for more detailed information.)

For Internet E-mail and Usenet newsgroup reply messages, the following
hidden text lines can be placed in the message text (authors should
obtain Internet technical documents [RFC-822] and [RFC-1036] for more
information):

#### `Subject:`

This should be an exact copy of the `Subject:` line from the message in
the mail packet.  Doors can compare this line to the subject text in the
"subj" field of `UPL_REC`, to determine if this field should be used in
place of the "subj" text.  This allows long subject lines to be
preserved, and is a requirement for proper message processing in Usenet
newsgroups.

#### `References:` (Usenet newsgroup areas only)

This should be an exact copy of the `References:` line from the message in
the mail packet, with the text of the `Message-ID:` line (if present)
appended to it.  If a `References:` line is not present in the original
message, readers should create one (using the contents of the
`Message-ID:` line).  This allows the `References:` line -- used by many
Usenet news readers to thread messages -- to be preserved, and is a
requirement for proper message processing in Usenet newsgroups.

#### `Newsgroups:` (Usenet newsgroup areas only)

This line lists the newsgroups to which the message will be posted.  If
a `Followup-To:` line is not present in the original message, the contents
of the original `Newsgroups:` line should be used (if present).  If a
`Followup-To:` line is present, the contents of that line should be used,
and the original `Newsgroups:` line discarded.  (This follows standard
practice for Usenet news readers.)

Readers can allow the user to alter the contents of this line, if
needed, so that the reply message will be posted (by the news server) to
multiple newsgroups.

#### `Followup-To:` (Usenet newsgroup areas only)

This line lists the newsgroups to which replies to this message should
be posted, and is completely optional. Readers can allow the user to
alter the contents of this line, if needed, so that when other users
reply to this message, it will be posted (by their news server) to the
newsgroups listed in this line.

#### `X-Mailreader:` (Internet E-mail areas only)  
#### `X-Newsreader:` (Usenet newsgroup areas only)

Identifies the reader which created the reply message. This line should
be appended to the end of the [RFC-822] header when generating the message
on the host system.

Other [RFC-822] header lines -- `From:`, `To:`, `Message-ID:`, etc. --
should not be included, as it is the job of the door to create these
lines when uploading the reply message to the host system.

The following details should be observed by doors when uploading
messages to the host system:

#### Local areas

No special circumstances required.

#### FidoNet NetMail areas

The `FMPT` and `TOPT` kludge lines should be added, as appropriate, so that
point messages are properly processed.  An `INTL` kludge line should be
added to messages destined for a different zone.  Adding a `MSGID` kludge
line is recommended, and a `REPLY` kludge line should be added if the
necessary information is in the `net_dest` field of `UPL_REC`.  Refer to
FidoNet technical documents [FTS-0001] and [FTS-0009] for more information.

#### FidoNet EchoMail areas

The `FMPT`, `TOPT`, and `INTL` kludge lines are not needed, and in fact are
illegal in EchoMail messages.  Adding a `MSGID` kludge line is
recommended, and a `REPLY` kludge line should be added if the necessary
information is in the `net_dest` field of `UPL_REC`.  A tear line and an
origin line should be appended to the message.  Refer to FidoNet
technical documents [FTS-0004] and [FTS-0009] for more information.

#### Internet E-mail areas

For host systems that require it, the complete [RFC-822] header should be
generated, with all necessary fields (including `From:`, `To:`, `Date:`,
and `Message-ID:`). Include the fields described earlier if necessary.

Some host systems may require more or less information; the door author
will have to code the door to handle things accordingly.

#### Usenet newsgroup areas

For host systems that require it, the complete [RFC-822] header should be
generated, with all necessary fields (including `From:`, `Date:`,
`Message-ID:`, and `Newsgroups:`). Include the fields described earlier if
necessary.  If a `Newsgroups:` line is not present in the reply message,
one should be created using information available from the host system.

Some host systems may require more or less information; the door author
will have to code the door to handle things accordingly.

Finally, door authors will need to handle such details as ANSI escape
sequences, ASCII characters in the range 128-255 (so- called "high
ASCII"), and so forth.  You should not rely on the reader to handle
them.


### Section 5.7  The *.REQ File

The *.REQ file consists of zero or more records, each containing the
name of a file which the user wishes to download from the host system.

The record is called `REQ_REC`, and contains the following field:

#### `filename`

The name of the file to download from the host system, in MS-DOS format.
The filename may contain wildcard characters ("*" and "?"), but doors
are not required to honor them.

In theory, the number of records which can be contained in a *.REQ file
is unlimited.  However, doors and readers may impose limits on the
number of files which the user may request.  For example, The Blue Wave
Offline Mail Door will not accept more than 10 files, and will ignore
any entries in the *.REQ file past the 10th record.

The fixed-length nature of the filename field in `REQ_REC` means that long
filenames, as supported by such operating systems as OS/2, Unix, MacOS,
Windows NT, and Windows 95, cannot be supported.  A future revision of
the Blue Wave specification will contain a replacement for the *.REQ
file, one which will allow the use of long filenames.


### Section 5.8  The *.OLC File

The *.OLC file is a text file that contains information with which the
mail door on the host system can be configured.  The file is in
Microsoft Windows INI format (refer to [Appendix B][apb] for a
description of this format), and consists of a single required section
for global information, and zero or more sections that indicate
configuration information for message areas.

The global information section uses the header:

#### `[Global Mail Host Information]`

The following keywords may appear in this section (any keyword that does
not appear indicate that the feature in question should be left
unchanged):

#### `MenuHotKeys=flag`

Specifies whether or not "hot keys" are to be used in the door's menus.
("Hot keys" indicate that the user does not have to press ENTER after
selecting a menu item.)

#### `ExpertMenus=flag`

Specifies whether or not expert mode is to be used in the door.  In
expert mode, only the menu prompt is displayed, as opposed to the full
menu.

#### `SkipUserMsgs=flag`

Indicates whether or not to bundle messages that were written by the
user.

#### `ExtendedInfo=flag`

Specifies whether or not to include hidden text lines in messages that
are bundled for download.

#### `NumericExtensions=flag`

Specifies whether or not to use numeric extensions for mail packets.  If
enabled, numeric extensions ("999") are used; if disabled, day of week
extensions ("SU1") are used.

#### `NewFileList=style`

Specifies whether or not a list of new files is to be generated when
creating a mail packet, and how that list is to appear.  "Style" can be
TEXT, ANSI, or OFF. If set to TEXT, the list is generated as a plain
ASCII text file.  If set to ANSI, the list will be colorized with ANSI
color sequences.  If set to OFF, the list is not generated.

#### `DoorGraphics=flag`

Specifies whether or not ANSI color sequences are to be used when
displaying output from the door.  If enabled, ANSI color codes are used;
if disabled, output is done using plain text.

#### `Password=type[,password]`

Sets the user's password and specifies the type of password to be used.
"Type" can be "No", "Door", "Reader", or "Both".  If "No" is specified,
the password is to be removed; otherwise, the password is to be set as
defined.

#### `MaxPacketSize=nnnK`

Specifies the maximum size (in kilobytes) of the download packet, BEFORE
archiving.  The value "nnn" can range from 0 to 32,767, with 0
indicating the packet can be of unlimited size.

#### `Filter=text`

Specifies the text to be used for a bundling filter. Theoretically,
there is no limit to the number of Filter statements that can be
included, though the door can limit how many it will accept (and
besides, the filter table in the *.INF file is limited to 10 entries).

If the door detects one or more Filter keywords in the *.OLC file, it
should erase all currently defined bundling filters and replace them
with the ones specified in the *.OLC file.

#### `Keyword=text`

Specifies the text to be used for a bundling keyword. Theoretically,
there is no limit to the number of Keyword statements that can be
included, though the door can limit how many it will accept (and
besides, the keyword table in the *.INF file is limited to 10 entries).

If the door detects one or more Keyword keywords in the *.OLC file, it
should erase all currently defined bundling keywords and replace them
with the ones specified in the *.OLC file.

#### `Macro=[Auto,]text`

Specifies the text to be used for a bundling macro.  If "Auto," appears
in front of the text, the macro will be considered an auto-execute
macro, which the door will execute immediately when the user chooses to
download a packet.

Theoretically, there is no limit to the number of Macro statements that
can be included, though the door can limit how many it will accept (and
besides, the macro table in the *.INF file is limited to three entries).

If the door detects one or more Macro keywords in the *.OLC file, it
should erase all currently defined bundling macros and replace them with
the ones specified in the *.OLC file.

#### `AreaChanges=flag`

Indicates whether or not the status of one or more message areas have
been changed.  See below for more details.

If the AreaChanges keyword is true, then the door will need to begin
processing message areas.  Each message area in the file has its own
section, with the echo tag for the message area serving as the section
header.  (Do not worry about conflicting with the global section header.
Echo tags are limited to 20 characters, and the global section header
text is much longer.)

The following keywords are currently allowed for a message area section:

#### `Scan=type`

Specifies how messages in the message area are to be bundled.  "Type"
can be "All" (all messages), "PersOnly" (only personal messages), or
"Pers+All" (only personal messages and messages addressed to "All").

When the door processes the areas in the *.OLC file, it must first mark
as inactive all areas for the user, then re-activate the ones specified
in the file.  The reader must make sure that the *.OLC file includes
sections for all areas which the user has kept active.  It's not the
most sophisticated system, true, but it's simple and easy to implement.


APPENDIX A:  OBSOLETE BLUE WAVE STRUCTURES
------------------------------------------

As the Blue Wave format has evolved, several files used in older
versions have become obsolete.  However, older versions of Blue Wave
applications may require that these files be present.  Thus, they are
described here, and included in the header file.  Note, however, that
expectations of backwards compatibility cannot be maintained forever,
and eventually, these structures will be dropped completely.


### Section A.1  The *.UPI File

The *.UPI file serves the same purpose as the current *.UPL file, the
exception being that it does not define reply messages for FidoNet
NetMail areas.  Since it does not contain the extended information
present in the *.UPL file, the *.UPI file can only be used for local and
FidoNet areas.

The *.UPI file consists of two parts: A single required header, and zero
or more reply records corresponding to each non-NetMail message
contained in the reply packet.  Each reply record includes the names of
the persons who wrote the replies and who they are addressed to, the
date and time the reply was written, and the name of the file containing
the text of the reply message.

Note that reply records do not have to appear in any particular order,
save for the fact that they must always follow the header.

The header record is called `UPI_HEADER`, and contains the following
fields:

#### `regnum`

Used by The Blue Wave Offline Mail Reader to store the registration code
of the reader.  Third-party authors should ignore this field.

#### `vernum`

The version number of the reader.  As a simple security measure, each
character of the version number is stored as the ASCII value plus 10.


The reply message record is called `UPI_REC`, and contains the following
fields:

#### `from`

The name of the person who wrote the reply message. This field should
only be used for message areas that allow any name to be entered in the
From field; otherwise, the user's name or alias (obtained from the host
system) should be used.

#### `to`

The name of the person to whom the reply message is addressed.

#### `subj`

The subject of the reply message.

#### `unix_date`

The date and time the message was written, stored Unix style as the
number of seconds since January 1, 1970. Universal Coordinated Time
(UTC) should be taken into account when storing and using this value
(the date and time functions in many C compilers handle this
automatically).

#### `fname`

The name of the file which contains the text of the reply message.  If
the file does not exist in the reply packet, or if this field is empty,
the door should consider the record to be invalid.

#### `echotag`

The tag for the message area on the host where this reply message should
be stored.  This field must contain the same text as the corresponding
field in `INF_AREA_INFO`, and is used by the door to ensure that the reply
message ends up in the proper area.

#### `flags`

Bit-mapped flags indicating the status of the reply message.  Refer to
the header file for a list of available flags.

#### `reedit`

Used by The Blue Wave Offline Mail Reader.  Third party applications
should ignore this field.

Readers should generate a *.UPI file only if the `uses_upl_file` field
in `INF_HEADER` is not set, AND the mail packet format is earlier than
version 3.  Doors should process a *.UPI file only if a *.UPL file is
not present.


### Section A.2  The *.NET File

The *.NET file serves the same purpose as the current *.UPL file, the
exception being that it only defines reply messages for FidoNet NetMail
areas.  It consists of one or more records, each containing the
information for a FidoNet NetMail reply message.

The NetMail reply message record is called `NET_REC`, and contains the
following fields:

#### `msg`

A structure containing the addressing information for the NetMail
message.  This structure is exactly the same as the header for the Fido
*.MSG file, and is described below.

#### `fname`

The name of the file which contains the text of the reply message.  If
the file does not exist in the reply packet, or if this field is empty,
the door should consider the record to be invalid.

#### `echotag`

The tag for the message area on the host where this reply message should
be stored.  This field must contain the same text as the corresponding
field in `INF_AREA_INFO`, and is used by the door to ensure that the reply
message ends up in the proper area.

#### `zone`

Contains the zone number of the destination FidoNet node address
(zone:net/node.point), as the Fido *.MSG header does not contain a field
for the zone number.

#### `point`

Contains the point number of the destination FidoNet node address
(zone:net/node.point), as the Fido *.MSG header does not contain a field
for the point number.

#### `unix_date`

The date and time the message was written, stored Unix style as the
number of seconds since January 1, 1970. Universal Coordinated Time
(UTC) should be taken into account when storing and using this value
(the date and time functions in many C compilers handle this
automatically).


The Fido *.MSG structure is defined in the header file as `MSG_REC`.  The
following fields in `MSG_REC` are of concern to doors and readers (the
rest can be ignored):

#### `from`

The name of the person who wrote the reply message. This field should
only be used for message areas that allow any name to be entered in the
From field; otherwise, the user's name or alias (obtained from the host
system) should be used.

#### `to`

The name of the person to whom the reply message is addressed.

#### `subj`

The subject of the reply message.

#### `dest`

The node number of the destination FidoNet node address
(zone:net/node.point).

#### `destnet`

The net number of the destination FidoNet node address
(zone:net/node.point).

#### `attr`

Bit-mapped flags indicating the NetMail attributes that should be
applied to the message.  Refer to the header file for a list of
available flags.

Readers should generate a *.NET file if the `uses_upl_file` field in
`INF_HEADER` is not set, AND the mail packet format is earlier than
version 3.  Doors should process a *.NET file only if a *.UPL file is
not present.


### Section A.3  The *.PDQ File

The *.PDQ file serves the same purpose as the *.OLC file, in that it
defines the necessary values to perform offline configuration of the
mail door.  The *.PDQ file consists of a single header, followed by zero
or more records defining the message areas that the user wishes to
enable for bundling.

The configuration file header is called `PDQ_HEADER`, and contains the
following fields:

#### `keywords`

The keywords used when bundling messages.  Up to 10 keywords may be
defined.

#### `filters`

The filters used when bundling messages.  Up to 10 filters may be
defined.

#### `macros`

The macros used to specify bundling commands.  Up to three macros may be
defined.

#### `password`

The user's password.

#### `passtype`

The intended use for the defined password.  0 indicates no password, 1
indicates a door password, 2 indicates a reader password, and 3
indicates a password for both the reader and the door.

#### `flags`

Bit-mapped flags indicating which door options should be enabled and
disabled.  Refer to the header file for a list of available flags.


The configuration file message area record is called `PDQ_REC`, and
contains the following field:

#### `echotag`

The echo tag of the message area that is to be made active for bundling.

If the `PDQ_AREA_CHANGES` flag in the "flags" field of `PDQ_HEADER` is
set, the door should deactivate all of the user's message areas, then
activate each area specified in the `PDQ_REC` records contained in the
*.PDQ file.

Readers should generate a *.PDQ file only if the mail packet format is
earlier than version 3; otherwise, a *.OLC file should be generated.
Doors should process a *.PDQ file only if a *.OLC file is not present.


APPENDIX B:  THE MICROSOFT WINDOWS INI FORMAT
---------------------------------------------

As mentioned earlier, the offline configuration file (*.OLC) used in the
reply packet is a text file that uses a format similar to the Microsoft
Windows INI format.  The greatest advantage to using this format, as
opposed to a binary file with fixed-length records, is that it can be
easily extended without breaking older applications.

The INI file format is quite simple.  It consists of one or more
sections, with a section consisting of a unique header name (in square
brackets, `[]`) followed by zero or more configuration lines.  These
configuration lines take the form of a keyword, followed by an equal
sign, followed by a value.  It is similar to the following:

```
[Header]
Keyword=Value
Keyword=Value
.
.
.
```

Keywords are case-insensitive.  The value itself can be a decimal
number, a string, or a true/false value, depending on the keyword and
what it configures.  True values can be specified as YES, ON, or TRUE;
false values can be specified as NO, OFF, or FALSE.

To obtain information from the file, you must first search for the
desired header.  (Strip any and all spaces and tab characters -- ASCII
9 -- that are present at the start of the line, as well as all such
characters before and after the equal sign on lines containing a
keyword.)  After the header has been found, each line that follows
should be read and examined.  If the line begins with a recognized
keyword, that line is processed accordingly.  If the line begins with a
left bracket (`[`), there are no more keywords in the section.

Keywords that are not recognized are to be ignored.  This is the key to
the format's extensibility, as new keywords can be added to the file in
future versions with the assurance that older versions will ignore the
new keywords.

The file can also contain any number of blank lines, as well as comment
lines.  If a line begins with a semicolon (`;`), it is a comment line
and should be ignored.


[cp]: #copyright-and-restrictions
[tr]: #trademark-notices

[ch1]: #chapter-1-introduction-and-overview
[s11]: #11-introduction
[s12]: #12-overview-of-the-developers-kit

[ch2]: #chapter-2-a-short-history-of-offline-mail
[s21]: #21-necessity-is-a-mother
[s22]: #22-the-pioneers-of-offline-mail
[s23]: #23-open-standards-and-the-blue-wave-format

[ch3]: #chapter-3-description-of-blue-wave-packets
[s31]: #31-filename-conventions
[s32]: #32-the-packet-id
[s33]: #33-the-blue-wave-mail-packet
[s34]: #34-the-blue-wave-reply-packet

[ch4]: #chapter-4-file-structure-technical-information
[s41]: #41-data-types
[s42]: #42-using-the-file-structures
[s43]: #43-using-the-structure-length-fields
[s44]: #44-unused-and-reserved-fields

[ch5]: #chapter-5-blue-wave-format-detailed-analysis
[s51]: #51-the-inf-file
[s52]: #52-the-mix-file
[s53]: #53-the-fti-file
[s54]: #54-the-dat-file
[s55]: #55-the-upl-file
[s56]: #56-reply-message-text
[s57]: #57-the-req-file
[s58]: #58-the-olc-file

[apa]: #appendix-a-obsolete-blue-wave-structures
[aa1]: #a1-the-upi-file
[aa2]: #a2-the-net-file
[aa3]: #a3-the-pdq-file

[apb]: #appendix-b-microsoft-windows-ini-format

[bluewave.inc]: bluewave.inc
[bluewave.h]: https://wmcbrine.com/MultiMail/mmail/bluewave.h

[FTS-0001]: https://nsrc.org/networks/fidonet/standards/fts-0001.016
[FTS-0004]: https://nsrc.org/networks/fidonet/standards/fts-0004.txt
[FTS-0009]: http://ftsc.org/docs/fts-0009.001

[RFC-822]: https://datatracker.ietf.org/doc/html/rfc822
[RFC-1036]: https://datatracker.ietf.org/doc/html/rfc1036

[William McBrine]: https://wmcbrine.com/
