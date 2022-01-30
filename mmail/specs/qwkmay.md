QWK mail format information
===========================

By Mark May - Mythical Kingdom Software

[This was originally circulated as an archive of nine very short text
files. - WJM3]

While writing a QWK utility, I found that there was no one place where
all the necessary info on the QWK format was available.  Thru messages
in several network echo conferences (Thanks to Tim Farley, Mark Goodwin,
Randy Blackmond, Mark Herring, Patrick Lee, Greg Hewgill, and others), I
gradually collected the information enclosed in this archive.

To save others the time collecting this information, I've put together
this set of files by editing the information from the various sources so
that it is conveniently available.

Tim Farley is working on a more rigorous specification and I'm sure all
of us would appreciate any help you can give him in this.  If you find
any errors or additional information, I'd also appreciate hearing about
it so that I can further update this set of files.


Changes
-------

- v1.1 08/17/91
    - Added information on Door.Id
    - Added info on conferences above 255
    - Added info on message has tagline field
    - Corrected info on message status values
- v1.0 05/28/91
    - Initial release


Filenames used by the *.QWK format
----------------------------------

BBSID.QWK is an archive containing the files from the message door being
sent to the caller.

### MESSAGES.DAT

   a file containing the messages themselves in 128 byte records.

### CONTROL.DAT

   a file with info on the system, caller, and conference names and
   numbers.

### 999.NDX

   one file for each selected conference that contains pointers to the
   messages in MESSAGES.DAT There are usually several *.NDX files in
   each QWK file.  (Right justified, padded with leading zeros to make
   the 3 characters).

### NEWFILES.DAT

   an optional file that contains a list of new files from the Bbs.

### BLT-0.99

   optional files containing ascii or ansi bulletins.  The 99 extension
   is replaced by the bulletin number (Left justified not padded).

### DOOR.ID

   an optional text file that contains info on the capabilities of the
   door that produced the QWK packet (so that the reader will only send
   control messages that can be handled).

### SESSION.TXT

   an optional ascii/ansi file containing info on the activity occuring
   in the mail door.

Optionally it may also contain ascii or ansi screens for Welcome, News,
and Goodbye as named in the CONTROL.DAT file.

BBSID.REP is an archive containing a single file of the messages sent
from the caller to the Bbs.

### BBSID.MSG

   a file containing the messages themselves in 128 byte records.  The
   format is similar to MESSAGES.DAT.


Format of Control.Dat file
--------------------------

Control.Dat is a normal CR/LF style text file with the following lines
in exactly the order shown.

```
BBS name
BBS City, BBS State
BBS phone number                     {AAA-EEE-NNNN}
Sysop name,Sysop                     {Sysop name in upper case followed
                                      by the literal ',Sysop'}
Serial number ,BBS ID                {Serial Number, UpperCase BBSID}
Date of mail packet, time of packet  {MM-DD-YYYY,HH:MM:SS}
Caller's name                        {Uppercase}
blank
0
0
Number of conferences (in additon to conference 0 which is main)
                      (ie use number of conferences minus one)
Conference number
Name of Conference    (should be limited to maximum length of 12 characters)
...
...
...
...
...
Conference number
Name of Conference
name of BBS welcome file
name of NEWS file
name of BBS goodbye file
```

Other optional data may occur after the Goodbye file line, but the
trend is to omit this data.  If included it is as follows:

```
0                                    {Unknown}
Screen Length
USER NAME                            {Upper case}
FirstName                            {Proper case}
CITY, ST                             {Upper case}
Data Phone                           {AAA EEE-NNNN}
Voice Phone                          {AAA EEE-NNNN}
Security Level
Expiration Date                      {MM-DD-YY}
Last On Date                         {MM-DD-YY}
Last On Time                         {HH:MM}
Number of calls
0                                    {Unknown}
DownLoaded Bytes
DownLoaded Count
Uploaded Bytes
Uploaded Count
Time Limit Per Day                   {Minutes}
Time Remaining                       {Minutes}
Time Used Today                      {Minutes}
DownLoad Limit/Day                   {Kilobytes}
DownLoad Bytes Remaining Today       {Bytes}
DownLoaded Today                     {Bytes}
Current Time                         {HH:MM}
Current Date                         {MM-DD-YY}
System Tag Line
```


DOOR.ID
-------

Many maildoors now produce (and readers use) a file called Door.Id that
was developed by Greg Hewgill.  It is intended to give the reader
information on the capabilities of the door that produced the packet
(and presumably will process the *.REP produced).  It is a straight text
file with the following format.  Each line is in the format ControlWord
space equals space value.

### `DOOR = <doorname>`

   This is the name of the door that created the QWK packet, eg.
   \<doorname\> = Tomcat.

### `VERSION = <doorversion>`

   This is the version number of the door that created the packet, eg.
   \<doorversion\> = 2.9.

### `SYSTEM = <systemtype>`

   This is the underlying BBS system type and version, eg.
   \<systemtype\> = Wildcat 2.55.

### `CONTROLNAME = <controlname>`

   This is the name to which the reader should send control messages,
   eg. \<controlname\> = TOMCAT.

### `CONTROLTYPE = <controltype>`

   This can be one of ADD, DROP, or REQUEST (or others). ADD and DROP
   are pretty obvious (they work as in Markmail), and REQUEST is for use
   with BBS systems that support file attachments.  Try out SLMR with
   CONTROLTYPE = REQUEST and use the Q function.

### `RECEIPT`

   This flag indicates that the door/BBS is capable of return receipts
   when a message is received.  If the first three letters of the
   subject are RRR, then the door should strip the RRR and set the
   'return-receipt-requested' flag on the corresponding message.


Format of the exported messages in Messages.Dat
-----------------------------------------------

This file contains records with a length of 128 bytes.  There are 3
types of these records: (1) Packet Header, (2) Message Header, and (3)
Message Text.  All unused fields in the records are normally filled with
spaces, although you will sometimes find the final Message text record
will be filled with nulls (#0) after the last text.

### Packet Header

Packet Header - is always the first record in the file and only occurs
once.  It contains only normal ascii text (limitted to at most 128
characters) and should always start with "Produced by ".  The remaining
text normally includes a product name and copyright message.

### Message Header

A message header immediately preceeds zero or more message text records.
Each Message header has the following format:

```
Start
 Pos  Length              Description
----- ------ -----------------------------------------
  1      1   Message status flag (see below)
  2      7   Message number (ascii left justified)
  9      8   Date (MM-DD-YY)
 17      5   Time (HH:MM)
 22     25   To (left justified space filled - uppercase)
             (Be sure to check the to field to spot
             configuration type messages)
 47     25   From (left justified space filled - uppercase)
 72     25   Subject (left justified space filled - uppercase)
             (a subject starting "NE:" should not be
             echoed into a network)
 97     12   Password (not really used leave blank)
109      8   Message reference number (ascii left justified)
117      6   Number of blocks (ascii left justified - number
             of 128 byte blocks including 1 for the message
             header)
123      1   Message active (ß or #225 = Active,
             Γ or #226 = Inactive)
124      2   Conference number (Binary Word) (Lo in 124, Hi
             in 125).  Note that some older programs only
             supported conferences 0 thru 255 and left byte
             125 as a "blank" unused field.  I recommend
             checking the word value and if it is over 8191
             to only use the lower (0 thru 255) part of it.
             I've also seen notes that this field is really
             a 4 byte LongInt field, although with the above
             limitation (and 8000 conference seeming sufficient
             for a while), I documented it this way.

128      1   Indicates whether the message has a Network
             tagline or not.  A value of "*" indicates that
             a network tagline is present, a value of " "
             indicates there isn't.  Messages sent to readers
             (non-net-status) generally leave this as always
             blank since only net utils need this info.
```

Message Status Flag has the following possibilities:

```
'`'  Comment to sysop, read      '~'  Comment to sysop, unread
'-'  Public, read                ' '  Public, unread
'*'  Private, read               '+'  Private, unread
'^'  Password protected, read    '%'  Password protected, unread
'#'  Group password, read        '!'  Group password, unread
'$'  Group password to all
```

### Message Text Records

The message text records immediately follow the the message header.
They contain straight ascii text (lines are normally limitted to 72
chars/line although you may see longer lines).  Each line is followed by
a "π" or #227 character to mark the end of the line (in place of the
normal CR/LF that would exist in a straight text file).  The text
continues consecutavly and text lines do continue across block
boundaries.  Some systems or readers may have problems with messages
longer than 99 lines or 199 lines, although more recently this no longer
seems to be a limit.  The last block should be padded with blanks to
fill the block, although on input you may find it padded with nulls
(#0).


Format of Index files
---------------------

Index files are named XXX.NDX (where XXX is the conference number padded
with leading zeros to make it three characters long.  There is one *.NDX
file for each conference chosen that contains messages in the
MESSAGES.DAT file.

The *.NDX file contain records five characters long that point to each
message in that conference.

    NdxRecord = Record
      MsgPointer: BasicReal
      Conference: Byte;
    End;

The BasicReal is a four byte number in BASIC MKS$ format.

The following is a sample program unit for TurboPascal that converts
between BasicReal format and LongInt format.

```
Unit BasicConvert;

Interface
  Function BasicReal2Long(InValue: LongInt): LongInt;
                {Convert Basic Short Reals to LongInts}

  Function Long2BasicReal(InValue: LongInt): LongInt;
                {Convert LongInts to Basic Short Reals}

Implementation

Function BasicReal2Long(InValue: LongInt): LongInt;

  Var
  Temp: LongInt;
  Negative: Boolean;
  Expon: Integer;

  Begin
    If InValue And $00800000 <> 0 Then
      Negative := True
    Else
      Negative := False;
    Expon := InValue shr 24;
    Expon := Expon and $ff;
    Temp := InValue and $007FFFFF;
    Temp := Temp or $00800000;
    Expon := Expon - 152;
    If Expon < 0 Then Temp := Temp shr Abs(Expon)
      Else Temp := Temp shl Expon;
    If Negative Then
      BasicReal2Long := -Temp
    Else
      BasicReal2Long := Temp;
    If Expon = 0 Then
      BasicReal2Long := 0;
  End;

Function Long2BasicReal(InValue: LongInt): LongInt;
  Var
  Negative: Boolean;
  Expon: LongInt;

  Begin
  If InValue = 0 Then
    Long2BasicReal := 0
  Else
    Begin
    If InValue < 0 Then
      Begin
      Negative := True;
      InValue := Abs(InValue);
      End
    Else
      Negative := False;
    Expon := 152;
    If InValue < $007FFFFF Then
      While ((InValue and $00800000) = 0) Do
        Begin
        InValue := InValue shl 1;
        Dec(Expon);
        End
    Else
      While ((InValue And $FF000000) <> 0) Do
        Begin
        InValue := InValue shr 1;
        Inc(Expon);
        End;
    InValue := InValue And $007FFFFF;
    If Negative Then
      InValue := InValue Or $00800000;
    Long2BasicReal := InValue + (Expon shl 24);
    End;
  End;

End.
```

A quick and dirty conversion (handles positive numbers only) is possible
with the following expression:

    MKSToNum := ((x AND NOT $ff000000) OR $00800000)
                 SHR (24 - ((x SHR 24) AND $7f));

The number contained in the MsgPointer is the record number (128 byte
records - starting numbering from record 1 not 0) of the message header.
Note that since the 1st record contains packet header, the lowest
MsgPointer that can exist is 2.

Some message readers will reformat the \*.NDX files so that the
MsgPointer becomes a LongInt fileseek position (using a record size of
1).  To determine which type of index you are reading you should look at
the size of the number.  Any BasicReal will appear as a huge number that
would unlikely ever be a byte seek positon.


Format of the BBSID.REP file for replies
----------------------------------------

The record format of the BBSID.MSG file in the BBSID.REP file is exactly
identical to the MESSAGES.DAT format except the packet header which now
is filled with blanks except that it contains the BBSID as the first few
characters.

On the Message Headers there is also one change.  The ascii field which
normally contains the message number is now filled (in ascii) with the
conference number of the message.  Note that some readers which place
the conference number here, neglect to fill in the normal Message Header
conference number field.


Old style configuration commands
--------------------------------

    To: QMAIL (or whatever)

Subject contains one of the following commands:

```
ADD                   Add current conference
ADD -20               Add current conference and set lastread pointer
                      20 below the end
ADD 9876              Add current conference and set lastread pointer
                      to message number 9876
DROP                  Drop current conference
RESET                 Reset current conference lastread pointer to
                      the end
RESET -20             Reset current conference lastread pointer to
                      20 below the end
RESET 9876            Reset current conference lastread pointer to
                      message number 9876
BLTS ON               Turn bulletins on
BLTS OFF              Turn bulletins off
FILES ON              Turn new files list on
FILES OFF             Turn new files list off
WELCOME ON            Turn welcome screen on
WELCOME OFF           Turn welcome screen off
GOODBYE ON            Turn goodbye screen on
GOODBYE OFF           Turn goodbye screen off
```

If the subject is CONFIG, see the new format below.


New (04/91) format for configuration messages
---------------------------------------------

    To: QMAIL (or whatever)
    Subject: CONFIG

Text has one command per line with the following options:

```
ADD <conf>                  adds a conference
DROP <conf>                 drops a conference
RESET <conf> <value>        resets message pointer to value or
                            can use HIGH-xxx
CITY <string>               changes bbs user city
PASSWORD <string>           changes bbs user password
BPHONE <string>             changes bbs user business/data phone
HPHONE <string>             changes bbs user home/voice phone
PCBEXPERT [ON|OFF]          turns bbs user expert mode on or off
PCBPROT <char>              sets bbs user protocol to char (A thru Z)
PCBCOMMENT <string>         sets bbs user comment
PAGELEN <value>             sets bbs use pagelength
PROTOCOL <char>             sets QWK door protocol to char (A thru Z)
EXPERT [ON|OFF]             sets QWK door expert mode on or off
MAXSIZE <value>             sets maximum QWK size in bytes
MAXNUMBER <value>           sets maximum msgs per conference
AUTOSTART                   QWK door autostart
```
