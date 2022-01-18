OMEN system
===========

Offline Message Environment  
(C) 1991 - 1995 by Omsoft  
Rev. I

Markdown by [William McBrine] on 18 January 2022

Offline Message Environment system (OMEN) is designed to be used with
any Bulletin Board Systems that have the capability to either pack
messages into a file and get new messages out of a file, or use an
outside program to do so. With OMEN you can minimize the online time you
spend in the BBS message boards. This is done by downloading all new
messages in a compressed file, reading and writing replies and new
messages locally at your own computer and uploading the results next
time in an other compressed file.

OMEN is not machine or operating system dependent, it can be implemented
virtually on any current system.  The original idea for offline message
system was introduced to me by Ari Laitinen of Arisoft and I have then
been developing it into an open structure.

The following structure is originally designed by Olli Majander of
Omsoft and hereby declared Public Domain.

---------------------------------------------------------------------


OMEN in a BBS - Overview
========================

OMEN can be implemented into Bulletin Board Systems that comply with the
following rules:

1. Message boards are numbered from 0 up to max. 65535
2. Board names are up to max. 80 characters long.
3. Messages are numbered from 1 up to max. 4294967295.
4. User names are max. 35 characters long.
5. Message subjects are max. 72 characters long.

Longer board names, user names and subjects must be truncated to the
mentioned lengths. Software compatible with earlier revisions of the
OMEN system may not be fully compatible with these rules.

OMEN can be implemented into the code of the BBS software or be run as a
separate program. The BBS-OMEN should pack up to maximum of 1000
messages into a file in near-ASCII format. The text and header
information of each message are pure ASCII, but some control bytes are
used to make it easier to tell header and text as well as other messages
apart. The messages should be stored into a file called NEWMSGxy.TXT.
The x and y stand for ID characters that are used to make sure that
correct packages reach the correct BBS. They are suggested to be
aphanumerical characters. It is up to each application to select the ID
characters.

Another file that is needed with OMEN is SYSTEMxy.BBS. The x and y are
the same as for the previous file. The system file should include the
name of the BBS, as well as information about all of the message boards
on which the user has rights to read or write messages.

Optional files that can be added to the package are:

```
NFILExy.BBS     Listing of new files
BULLETxy.BBS    System bulletin
BNAMESxy.BBS    Names of the boards in system
INFOxy.BBS      Additional information for the reader
```

All the files should be compressed with some compressing utility, for
instance PKZIP, which would result in OMENxy.ZIP. This will be the file
that is sent to the user with whatever protocol the system allows.

If the ID characters for the system are for example R and 7, the
corresponding filenames would then be: SYSTEMR7.BBS, NEWMSGR7.TXT,
NFILER7.BBS, BULLETR7.BBS, BNAMESR7.BBS, INFOR7.BBS and for the complete
package OMENR7.ZIP.

BBS-OMEN must be able to receive a file called RETURNxy.ZIP (or other
compressed file). Inside it will be files, that contain the messages the
user has written offline and possibly other functions as well. The
private bit should only be used on boards that are allowed to contain
both public and private messages.

The files containing the messages will be described later in this
document.

A complete structure of the files included in OMENxy package follows.

NOTE: String[40] means an Pascal-style ASCII string with space for 41
bytes, where the first byte indicates the actual length of the string to
follow. Byte is an 8-bit unsigned character and word is a 16-bit
unsigned integer.


SYSTEMxy.BBS:
-------------

```
  SystemName:    String[40];
  BoardRights:   Array[0..?] of BrdRecord;

BrdRecord:
  BrdNum:        Byte;  /* Low byte of Board number */
  BrdStatus:     Byte;  /* See below */
  BrdHighNum:    Byte;  /* High byte of Board number, See below! */
  BrdName:       String[16];
```

Due to compatibility reasons the high byte of the board number is
indicated in the third byte of the BrdRecord.

```
BrdStatus:

  Bit 0:  Write rigths
  Bit 1:  SysOp rights
  Bit 2:  Private board
  Bit 3:  Public board
  Bit 4:  NetMail board
  Bit 5:  Use of Alias allowed on board
  Bit 6:  Board selected by user
  Bit 7:  Reserved for future use
```

Bit 5 is used only, if the alias is user selectable. If the alias is
fixed by the software, the board should be flagged as a normal board and
the fixed alias used instead of the real name.


NEWMSGxy.TXT:
-------------

```
  MsgBase:       Array[0..?] of MsgRecord;
  EOF:           Byte = 26;

MsgRecord:
  SOH:           Byte = 1;
  MessageHeader: MsgHdrRecord;
  STX:           Byte = 2;
  MessageText:   MsgTxtRecord;
  ETX:           Byte = 3;

MsgHdrRecord:
  NumSign:       Byte = '#';
  MsgNum:        Messagenumber in ASCII;
  TwoSpace:      "  ";                  /* 2 consecutive ASCII 32's */
  BoardNum:      BoardNumber in ASCII;
  Colon:         Byte = ':';
  Brdname:       Name of the board in ASCII;
  TwoSpace:      "  ";
  Date:          Creation date of the message;
  TwoSpace:      "  ";
  Time:          Creation time of the message;
  TwoSpace:      "  ";
  Chain:         Previous and Next message in chain;
  TwoSpace:      "  ";
  Status:        Private & Received -status;
  CrLf:          ASCII 13 + ASCII 10;
  WhoFrom:       Writer's name in ASCII;
  Separator:     " => ";                /* String to separate names */
  WhoTo:         Receiver's name in ASCII;
  CrLf:          ASCII 13 + ASCII 10;;
  Subject:       Subject of message in ASCII;

MsgTxtRecord:
  MessageText in ASCII;
```

Messages must be placed in the file so that messages on each board are
in sequence. The boards are recommended to follow ascending numerical
order but this is not mandatory.

The message header should not contain more than 3 lines. Recommended
maximum lenght for the name of the board in the header is 30 characters.
Longer names may result in the line exceeding 80 characters and this may
cause problems with some of the readers.

The Date string should follow the format of dd-mmm-yy, where dd is the
date, mmm is 3 character abbreviation of the month (e.g. Jan, Feb...)
and yy is the 2 last digits of the year.

The Time should have the format hh:mm where hh is the hour in 24-hour
format and mm is the minutes.

Chain consists of the number of the previous and the next message in
reply chain in parenthesis separated by slash. If no previous or next
message exist, the number must be replaced by dash. For exapmle a
message with a previous message but a reply would have this kind of
chain indicator: (-/2653).

Status consists of indicators for the private and received status of the
message. Private message is indicated by an uppercase P and received
message by uppercase R in parenthesis. Possible combinations are: (PR),
(P), (R) and ().

An examplary header follows:

```
<01>#12345  5:Communication Board  12-Aug-92  21:15  (12340/12352)  (PR)
John Doe => Archibald Leach
Subj: What's cooking Cary?
```

With NetMail messages the matrix address of the writer may be added
after the name of the receiver as From: (xx:xxx/xxx) separated from the
name by a single space.

The end of the subject, the STX byte, the actual text of the message and
the ETX byte should follow each other immediately. The text itself must
not contain any other characters below ASCII 32 except TAB, LF and CR
(09, 10, 13).


NFILExy.BBS:
------------

This file should contain the names, sizes and descriptions of the new
files in the system. The file must be pure ASCII. This file is optional.


BULLETxy.BBS:
-------------

This file contains any bulletins the operator of the system wants the
users to read. The file must be pure ASCII. This file is optional.


BNAMESxy.BBS:
-------------

This file contains the names of each board reprisented in the SYSTEMxy
file. The names are presented with the number of each board followed by
a colon followed by the name of the board in ASCII. The names must be
presented in the same order as they appear in the SYSTEMxy file.

Example:

```
1:General
2:Private Mail
3:SysOp Only
```

Each name can be up to 80 characters long. NOTE: Some of the reader
software may not support longer than 16 characters long board names.
This file is optional and needed only in systems with longer board names
than 16 characters.


INFOxy.BBS:
-----------

This file contains additional information for the reader software. The
file must be pure ASCII. The overall format of the file is:

`<label>:<option>`

The following labels have been currently defined:

```
Labels:         Options:        Description:

ORIGIN          -               Name of the program that created the
                                messagefile

SYSOP           -               Name of the system operator

C_SET           {IBM|ISO|SF7}   Preferred character set in the system

MSGNUM          {BOARD|BASE}    Indicates whether the system
                                numbers the messages board by board
                                or the whole base in one sequence

CHAINS          {ON|OFF}        Indicates whether the messages in
                                the file are packed following the
                                reply chain (ON) or in numerical
                                order (OFF).

SELECT          {ON|OFF}        Indicates whether the system supports
                                offline board selections.
```

This file is optional and all of the reader software do not use the
information provided in this file.

---------------------------------------------------------------------


OMEN at home - Overview
=======================

The offline OMEN can be implemented into the terminal software or be
used as a separate program. The program should provide an interface for
the user to read the new messages sent by BBS-OMEN. Replying to these
messages, writing completely new messages and possibly performing some
additional functions should be made available for the user.

The functions and information of the message affected by each function
are saved in a file called HEADERxy.BBS. The x and y characters should
be taken from the OMENxy.ZIP file (or other compressed file) that was
sent by BBS-OMEN.

The actual text of a reply or a new message should be pure ASCII, lines
not longer than 70 characters and terminated with a CrLf sequence. The
text is saved in a file called MSGxynn.TXT. The nn is the number of
message relative to the header block position starting from zero. Single
digit numbers will be padded with a leading zero. Up to 100 new messages
can be transmitted to the BBS-OMEN at one connection, numbered as
MSGxy00.TXT - MSGxy99.TXT. Any additional messages should be rejected.

Only one function should be permitted to be operated on single message.
Unused command bits, other unused portions of header block and the
Extra Space bytes should be set to zero.

Optional files that can be added to the package are:

```
SKIPxy.PST      Unwanted subjects
TRASHxy.PST     Names of unwanted writers
```

The HEADERxy.BBS, optional SKIPxy.PST and TRASHxy.PST files, and all of
the MSGxynn.TXT files must be compressed with PKZIP or alike to a file
called RETURNxy.ZIP (extension varies), which will then be sent back to
the BBS-OMEN.

A structure of the files follows:

HEADERxy.BBS:
=============

```
  Array[1..?] of ActionRecord;

ActionRecord:
  Command:       Byte;          /* See below! */
  CurBoard:      Byte;          /* Original board of message (Low byte) */
  MoveBoard:     Byte;          /* Board to move message (Low byte of
                                   MoveBoard/High byte of CurBoard),
                                   See below! */
  MsgNumber:     word;          /* Message to operate on  (low 16-bits) */
  WhoTo:         String[35];
  Subject:       String[72];
  DestZone:      word;           /* Destination NetMail Zone */
  DestNet:       word;           /* Destination NetMail Net */
  DestNode:      word;           /* Destination NetMail Node */
  NetAttrib:     Byte;           /* See below! */
  Alias:         String[20];     /* Alias, if used */
  CurHighBoard:  Byte;           /* High byte of CurBoard, See below! */
  MoveHighBoard: Byte;           /* High byte of MoveBoard, See below! */
  MsgHighNumber: word;           /* High 16-bits of the message number */
  ExtraSpace:    Array[1..4] of Byte;

Command:

  Bit 0:  Save Message
  Bit 1:  Delete Message
  Bit 2:  Toggle Private/Public
  Bit 3:  Move message
  Bit 4:  Make message private
  Bit 5:  Alias used with message
  Bit 6 - 7:  Reserved for future use
```

Only bits 0 & 4 and 3 & 4 can be combined allowing the message to be
made private when written on a board which is both for public and
private messages as well as when a message is moved to another board. 0
& 5 is also allowed when message is to be saved with an alias.

```
NetAttrib:

  Bit 0:  Kill after sent
  Bit 1:  CrashMail
  Bit 2 - 7:  Reserved for future use
```

Information placed in the header block for each operation:

New messages: Bit 0 of the Command byte must be set. If the message is
written on a board that contains both public and private messages, Bit 4
of the Command byte must be set according to the status of the message.
WhoTo field must contain the name of the receiver or "All" if message is
not addressed to anyone perticular. Subject field must contain the
subject of the message. DestZone, DestNet and DestNode must contain the
matrix address of the receiver if the message is written on a
NetMail board.

NOTE! If board number exceedes 255, high byte of the number is placed in
the MoveBoard field.

Replies: as above, but MsgNumber must contain the number of the message
to which this is a reply.

Deleting: Bit 1 of the Command byte must be set. CurBoard must contain
the number of the board the deleted message is on. MsgNumber must
contain the number of the deleted message. WhoTo field must contain the
name of the writer of the deleted message. No corresponding MSG file
should be made for the headerblock.

NOTE! If board number exceedes 255, high byte of the number is placed in
the MoveBoard field.

Pri/Pub Toggle: as above but Bit 2 of the Command byte must be set
instead.

NOTE! If board number exceedes 255, high byte of the number is placed in
the MoveBoard field.

Moving: as above, but Bit 3 of the Command byte must be set instead.
MoveBoard must contain the number of the board on which the message is
to be moved. Bit 4 of the Command byte must be set if the message is to
be private after moving. Subject field may contain a new subject for the
message.

NOTE! If either CurBoard of MoveBoard exceedes 255, high bytes are
placed in the CurHighBoard and MoveHighBoard fields.


MSGxynn.TXT
-----------

Each file contains the ASCII text of a message. The number nn is the
number of the header block relative to the message, starting from zero
and padded with a leading zero for the first 10 messages.


SKIPxy.PST
----------

```
  Array[1..?] of Subject;
```

This file contains the subjects that the user does not wish to be packed
in the next message file. The subjects must be in the string[72] format
without the "Subj: " prefix. Recommended maximum amount of subjects is
100. Subject skipping can only be done if the RETURNxy file is uploaded
to the BBS-OMEN immediately before message packing.

NOTE: All the BBS-OMENs may not support the message skipping by
subjects.


TRASHxy.PST
-----------

This file contains the names of the users whose messages are not wanted
to be packed in the message file. The names must be in pure ASCII
followed by CrLf, one name on each line. Recommended maximum amount of
names is 50. Subject skipping can only be done if the RETURNxy file is
uploaded to the BBS-OMEN immediately before message packing.

NOTE: All the BBS-OMENs may not support message skipping by writers.


WORDSKIP.PST
------------

This file contains keywords, one word or string on a line followed by
CR+LF. Messages with subjects containing any of these keywords are not
packed in the message file.

NOTE: All the BBS-OMENs may not support message skipping by keywords.


SELECTxy.CNF
------------

This file contains the numbers of boards user wants to select. This file
is pure ASCII and each board number is on its own line followed by
CR+LF.

NOTE: All the BBS-OMENs may not support offline board selection. Support
is indicated in the INFOxy.BBS file.


[William McBrine]: https://wmcbrine.com/
