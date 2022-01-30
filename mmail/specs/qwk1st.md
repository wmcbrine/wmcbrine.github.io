The Programmer's Guide to 1stReader for DOS (excerpt)
=====================================================

Release 2.00

Entire work Copyright 1995 by Mark Herring  
All Rights Reserved


TABLE OF CONTENTS
-----------------

- [Introduction][i0]
- [The QWK format][q0]
    - [Name and extension][q1]
    - [Files stored in a QWK mail packet][q2]
    - [Format for CONTROL.DAT][q3]
    - [Format of MESSAGES.DAT][q4]
    - [Format of the INDEX files][q5]
    - [Format for ATTACHED.LST][q6]
- [The REP format][r0]
    - [Format for \<QWK ID\>.MSG][r1]
    - [Format for ATTXREF.DAT][r2]


Introduction
============

This manual was written for programmers who would like to add utilities
and other programs into the 1stReader environment. Advanced 1stReader
users may also find it useful in helping to understand how the 1stReader
Applications Program Interface (API) works.

It is assumed that the reader is advanced in the ways of programming and
using 1stReader.

1stReader was compiled using Microsoft's QuickBasic 4.5 compiler system.
So many of the conventions in this manual reflect a QuickBasic
background.  If you use a different computer language it is up to you to
handle the conversions correctly.


The QWK format
==============

Before we go into the details of the 1stReader API it might be of some
benefit to review what makes up the QWK format.  The QWK format was
created in 1987 by Mark "Sparky" Herring for a friend who moved to a
small town in Texas.  The idea was to gather up all of the new messages
posted on a bulletin board system and "quickly" transmit them to the
user.  Then the user could disconnect from the BBS and read the messages
at their leisure using a QWK mail reader.  If the user entered any new
messages they could call the BBS back and upload their replies to the
system.

Messaging volume took off as soon as QWK was introduced to the public.
Suddenly, users could read a much larger number of messages "offline"
than they could "online".  Users also found they could call bulletin
board systems that were long distance and keep up with messages without
spending a fortune.  Sysops liked the QWK format because it allowed them
to service more users per hour per node than without it.

Today, the QWK format is literally world-wide and is used by every major
bulletin board software system.  Millions of people download QWK mail
packets daily to read answer their messages.

Through the years there have been extensions and additions made to the
QWK format.  We will only discuss the QWK format as it is supported by
1stReader.


Name and extension
------------------

The sysop of every bulletin board system that produces a QWK mail packet
must have designated their own "QWK ID" for the BBS.  The QWK ID is
simply the name of the QWK mail packet without the QWK extension.  If
the mail packet is named "SPARKY.QWK" then the QWK ID for the BBS is
simply "SPARKY".  The QWK is always eight characters or less, uses UPPER
case letters and never includes a space, asterisk or question mark.  It
is used as the filename of the QWK mail packet so it must conform to
standard DOS file naming conventions.

The extension of the QWK mail packet is always ".QWK".  However, if the
user has more than one QWK mail packet on file for the same QWK ID then
the second QWK mail packet uses the extension of ".QW0".  The third
packet uses ".QW1".  The fourth packet uses ".QW2", etc.  Using this
naming convention the QWK format supports up to 101 QWK mail packets on
file for the same QWK ID.

Some QWK offline mail readers change the name of the QWK ID in the QWK
mail packet to increment different packet names.  This method is wrong
because it changes the QWK ID of the system and makes it harder to
locate all of the packets associated with the current QWK ID.

A QWK mail packet is almost always compressed using the ZIP format.
Some bulletin board systems may use other file compression formats to
produce their own QWK mail packets and this is acceptable.  But, by and
large, most systems today use the ZIP format to pack the files contained
inside a QWK mail packet.


Files stored in a QWK mail packet
---------------------------------

There are many different files that can be stored inside one QWK mail
packet.  The minimum files required by 1stReader are:

### CONTROL.DAT

   This file contains information about the BBS such as name, telephone
   number and conference names.

### MESSAGES.DAT

   This file stores the actual messages to be read by the user.

### *.NDX

   Index files.  The "*" is replaced by the conference number.  These 
   files point to individual messages stored in the MESSAGES.DAT file.

---

Other files can be included in a standard QWK mail packet. These files
include:

### WELCOME

   This file is the welcome display for the BBS.  It is displayed 
   immediately after 1stReader

### BBSNEWS

   Displays any news from the sysop to the user.  1stReader displays
   this file immediately after the WELCOME file.

### GOODBYE

   This file is displayed to the user when they decide to close the
   current QWK mail packet and return to the 1stReader menu.

### ATTACHED.LST

   If the QWK mail packet included any attached files then this file 
   stores their filenames.

---

If the BBS is using a Qmail Door from Sparkware these files can also be
included in the QWK mail packet:

### OPTION.QM4  

   Stores a list of options available to the user when they use
   1stReader's "DOOR" button when reading messages.

### SERVICE.QM4

   Stores a list of services available to the user when they use
   1stReader's "DOOR" button when reading messages.

### SERVICES.DAT

   This file stores a list of services that were included by Qmail Door
   in the QWK mail packet.


Format for CONTROL.DAT
----------------------

CONTROL.DAT is a text file with the following format:

    Line#  Description
    -----  -----------
      1    Name of the bulletin board system (40 characters)
      2    Blank
      3    Bulletin board's telephone number
      4    Name of the sysop
      5    Qmail Door serial number and QWK ID.  These two fields are
           separated by a comma.  The QWK ID is always stored in UPPER
           case letters.
      6    Date and time the QWK mail packet was created, separated by
           commas.
      7    User's login name
      8    Name of Qmail DeLuxe² menu file.  This file was displayed to
           users as a backdrop for the menu. 1stReader ignores this entry.
      9    Normally zero.  If this entry contains "-1" then the user is
           also the sysop.  This grants no special privs in our Qmail Door.
           The value is only present for the QWK reader's needs (if any).
     10    Usually zero, but if used it stores the total number of messages
           in the QWK mail packet.
     11    Number of conferences stored in CONTROL.DAT

The next lines in CONTROL.DAT store the conference number on the first
line followed by the conference name on the next line.  This is repeated
for as many conferences as stored in CONTROL.DAT.

After the name of the last conference in the list in CONTROL.DAT the
following lines are used:

   - Name of the BBS welcome file.
   - Name of the BBS news file.
   - Name of the BBS goodbye file.
   - Number of alias names stored in CONTROL.DAT
   - Alias #1 (if used)
   - Alias #2 (if used)


Format of MESSAGES.DAT
----------------------

MESSAGES.DAT is a binary file with a record length of 128 bytes.

The first record in MESSAGES.DAT contains the copyright notice "Produced
by Qmail...Copyright (c) 1987 by Sparkware.  All Rights Reserved".

The first message in the file actually begins with LRN (logical record
number) #2.  It is here that you find the first message's "header".
This header record tells 1stReader about the message that follows.  The
format for a message header is:

    TYPE QWKType
        Status AS STRING * 1     'Status of message
        Number AS STRING * 7     'Message number
        Date AS STRING * 8       'Date
        Time AS STRING * 5       'Time
        MsgTo AS STRING * 25     'To
        MsgFrom AS STRING * 25   'From
        Subject AS STRING * 25   'Subject
        Password AS STRING * 12  'Password
        Reference AS STRING * 8  'Reference number
        Blocks AS STRING * 6     'Number of blocks
        Delete AS STRING * 1     'Current status of message
        Conference AS INTEGER    'Conference
        Blank AS STRING * 2      'Blank
        Tag AS STRING * 1        'Message tagline
    END TYPE


"Status" is a string variable of one byte's length.  It can contain one
of the following values:

   - PUBLIC message (message that anyone can read):
       - ( ) - A message that has not been read
       - (-) - A message to a specific person which has been read
   - PRIVATE message (message that only the sender or receiver can read):
       - (*) - A message that has not been read
       - (+) - A message which has been read by the addressee
   - COMMENT to the sysop:
       - (-) - A comment to the sysop which has not been read
       - (`) - A comment to the sysop which has been read
   - SENDER PASSWORD (message that require a password to delete them):
       - (%) - A message which has not been read
       - (^) - A message which has been read by the addressee
   - GROUP PASSWORD (message that require a password to read them):
       - (!) - A message which has not been read
       - (#) - A message which has been read by the addressee
       - ($) - A message which was addressed to ALL

"Number" is the message number as it exists on the bulletin board
system.  This field is seven bytes in length and is left justified.

"Date" and "Time" store the date and time the message was written to the
bulletin board system.

"MsgTo" is a 25 byte field that stores the name of the user to whom the
message was addressed.

"MsgFrom" is a 25 byte field that stores the name of the author of the
message.

"Subject" is a 25 byte field that stores the subject of the message.

"Password" is a 12 byte field that contains the password of the message,
if any.  Note that 1stReader does not require a password in order to
read the message since it is the mail door's responsibility to insure
that you only receive messages that YOU can read.

"Reference" is an eight byte field.  If this message is a reply to a
previous message then this field stores that previous message's number
on the BBS.

"Blocks" stores the number of 128 byte records used to store this
message.  Note that the message *is* counted as one of these blocks.

"Delete" is usually set to ASCII 225 to indicate that the message has
not been deleted.  If the message is deleted then this value stores
ASCII 226.

"Conference" is a two byte INTEGER field that stores the conference
number that contains this message.  For example, if this message was
found in conference #100 then this field will store "100" (in a signed
INTEGER format).

"Blank" is set to NULLs.

"Tag" is set to "*" if the message was processed by our own QNet mail
tosser.

The text of the message always follows the message header. ASCII 227 is
used as the delimiter between lines of text.  This code segment should
help you understand how to read a message in MESSAGES.DAT:

    DIM Qwk AS QWKType
    Byte128$=STRING$(128,0)
    OPEN "MESSAGES.DAT" FOR RANDOM AS #1 LEN=128
    Record=2
    GET #1,Record,Qwk
    PRINT " Number:",Qwk.Number
    PRINT "     To:",Qwk.MsgTo
    PRINT "   From:",Qwk.MsgFrom
    PRINT "Subject:",Qwk.Subject
    PRINT
    Buffer$=""
    FOR i=1 TO VAL(Qwk.Blocks)-1
        GET #1,Record+i,Byte128$
        Buffer$=Buffer$+Byte128$
        DO
            In=INSTR(Buffer$,CHR$(227)
            IF In>0 THEN
                PRINT LEFT$(Buffer$,In-1)
                Buffer$=MID$(Buffer$,In+1)
            END IF
        LOOP WHILE In>0
    NEXT
    CLOSE #1


Format of the INDEX files
-------------------------

The .NDX files are used by 1stReader to locate individual messages in
each conference.  The name of each NDX file is used by 1stReader to
determine the conference number.  A filename of 050.NDX tells 1stReader
that messages stored in the index belong to conference #50.  As we
discussed earlier, CONTROL.DAT stores the name of the conference so
1stReader puts the two together to identify both the conference name and
its messages.

Any conference number less than 100 uses a leading ZERO to pad out the
INDEX filespec.

INDEX files use a five byte logical record length so it is easy to find
out how many messages are stored in one conference. Just divide the size
of the INDEX file by FIVE and you will arrive at the number of messages
stored in the conference.

The first four bytes of each INDEX record are a Microsoft Float Point
Single Precision Format variable that contains the record number of the
associated message header in MESSAGES.DAT.  If you are using QuickBasic
this value can be read by using the CVS() function.  The fifth byte is
unused by 1stReader.

If 1stReader wanted to read the third message in a conference it would
first open the INDEX file for the conference and read the third record
in the INDEX file.  From there it would find the pointer in the first
four bytes of the record and using the pointer would read the record
number POINTER in MESSAGES.DAT to bring the message into the buffer.


Format for ATTACHED.LST
-----------------------

The file ATTACHED.LST lists the real name of attached files and uses the
following format (one line per entry):

    [conference #],[message #],[filename1],[filename2]

    [conference #]      Conference where the request came from
    [message #]         Message number where request came from
    [filename1]         File as named inside the QWK mail packet
    [filename2]         Original filename as stored on the BBS


The REP format
==============

The REP format follows the QWK format very closely.  It is used by
1stReader to store any replies or new messages the user enters with
1stReader.  Later, this REP reply packet can be uploaded back to the BBS
and its messages inserted into the correct conferences.

The REP reply packet also can store any files the user attached to
messages.  If attached files are included then the REP reply packet will
include a ATTXREF.DAT file.

The REP reply packet is created using the same archiver method used to
create the QWK mail packet.


Format for \<QWK ID\>.MSG
-------------------------

Each REP reply packet contains a .MSG file that uses the QWK ID of the
current system as its filename.  This was done to prevent a user from
accidentally uploading a REP reply packet for one BBS to another BBS.

The .MSG file uses the same header record defined earlier for
MESSAGES.DAT.  There are only three differences between messages stored
in MESSAGES.DAT and the .MSG file:

   1. The first record of the .MSG file contains the QWK ID for the
      current system in UPPER case letters.  This was done to provide a
      second backup to prevent a user from accidentally uploading a REP
      reply packet for one BBS to another BBS.

   2. The destination conference for the reply (or new message) is
      stored in the "Number" field.  If the reply was to be uploaded
      into conference #50 then the "Number" field stores "50" (left
      justified). The "Conference" field also stores the destination
      conference too.

   3. The .MSG file does not use an INDEX file.  Rather, 1stReader scans
      the .MSG file each time the user joins the REPLIES conference and
      builds an index in memory.  Since the file is dynamic, using an
      INDEX file would just complicate matters.

If a message is deleted in the .MSG file then the "Deleted" variable
will contain an ASCII 226.


Format for ATTXREF.DAT
----------------------

ATTXREF.DAT file is a text file that stores the names of any attached
files included in the REP reply packet.  If there are, for example, ten
replies on file then ATTXREF.DAT will contain ten lines of mostly empty
text.  If the third message in the .MSG file included an attached
message then the third line of ATTXREF.DAT would store information about
the attached file using this format:

    Internal filename,Real filename

"Internal filename" is the name of the attached file as it is stored in
the REP reply packet.  "Real filename" is the name of the file as it
should be renamed once the REP reply packet is uploaded back to the BBS.

Note that the QWK mail system on the destination BBS handles the
renaming of the files.


[i0]: #introduction
[q0]: #the-qwk-format
[q1]: #name-and-extension
[q2]: #files-stored-in-a-qwk-mail-packet
[q3]: #format-for-controldat
[q4]: #format-of-messagesdat
[q5]: #format-of-the-index-files
[q6]: #format-for-attatchedlst
[r0]: #the-rep-format
[r1]: #format-for-qwk-idmsg
[r2]: #format-for-attxrefdat
