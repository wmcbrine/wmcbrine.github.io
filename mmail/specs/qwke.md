QWKE Specifications 1.02
========================

(also known as EQWK - pronounced Quicky, and E-quick respectively)  
Copyright 1994-1997, Peter Rocca

This documentation may be freely used to enhance/create 1st, 2nd or 3rd
party applications or utilities without any legal obligation to the
author. Please do not extend or further the QWKE without contacting Pete
Rocca, in order to keep the standard from becoming a 'non-standard'. -
Thank you.

The only differences to the standard QWK format lay in four files.

**CONTROL.DAT**
: conference area names are no longer limited to 13 characters, they are
  limited to 255 characters now.

**DOOR.ID**
: contains additional CONTROLTYPE names.

**TOREADER.EXT**
: a file created by the door that contains additional information for
  the reader.

**TODOOR.EXT**
: a file created by the reader that contains additional information for
  the door.

In addition, certain kludge lines should be imported to messages when
downloading, if their length exceeds 25 characters.

These kludges are...

    To:
    From:
    Subject:

...they should be at the top of the message, and terminated by either a
carriage return, or the 'π' QWK terminator. At the end of the last
imported kludge, there should be a blank line, however you should program
to handle if one is not present. Additional kludge names can be created,
but your reader/door should at least handle these three.

Also, the kludge lines To/Subject should be imported into the message
fields when posting the messages (uploading). The From kludge can be
accepted, but remember to check the security of it (ie make sure that the
name is allowed, ie alias base, etc). Also, regarding the To: kludge...
keep in mind that UUCP software likes the To: line left in the first line
of a message, therefore it should not be removed from the message if this
message is destined to be sent through an UUCP gateway.

A sample message with kludge lines might look like:

```
     1       10        20   25
     |---+----|----+----|----|
  TO:PETER ROCCAZISKINZIDONING
FROM:BOB WILIKERS
SUBJ:This is a test of the sys                       <--- HEADER
     --------------------------------------------------------------------
     To: PETER ROCCAZISKINZIDONINGLY                 <--- MESSAGE TEXT
     Subject: This is a test of the system, as you can see!
     <blank line>
     The real message starts here now, but it was short, so
     see you later.
```

As you can see, only the To/Subject lines were imported, since the From
line did not exceed the 25 character limit.

One common question is NETMAIL? How do you do it? A standard that I would
suggest is the "Subject line method" which is simply an "at" symbol,
followed by the full netmail address. For example to send mail to Pete
Rocca at 1:2401/305.200, the message header would look like this:

    To: PETE ROCCA
    Fm: JOE USER
    Sb: @1:2401/305.200

How about internet mail?

Well, internet mail going out a fido gateway would simply be a netmail
message with a `To: <address>` on the first line. It is up to the mail
door not to import a "To:" kludge when sending internet netmail mail. For
example (Fido->Internet):

    To: UUCP
    Fm: JOE USER
    Sb: @1:1/31
    ------------------------------------
    To: support@mbcc.com
    Subject: Hello there

A regular internet message would simply be a regular message, but with a
"To" kludge to avoid the 25 character limit in the To field. For example
(regular Internet):

    To: INTERNET                         <--- this gets replaced on import
    Fm: JOE USER
    Sb: Hello there
    ------------------------------------
    To: support@mbcc.com

Of course, as a door or reader author, you can make this completely
transparent to the user.

---


TOREADER.EXT
------------

This file is simply an ASCII text file that is included in the QWK packet,
and contains information used by the QWK reader (created by the mail
door).

Each line has an identifier, followed by the relevant information for the
extended information. (Arguments in `< >` are required, and ones in `[ ]`
are optional.)

    ALIAS     <users' alias name>
    AREA      <conference#> <settings>
    BULL      <filename> <description>
    ATTACH    <filename> <conference#> <message#>
    FILE      <filename> [description]
    KEYWORD   <keyword>
    FILTER    <filter>
    TWIT      <twit>


### Identifier: `ALIAS <users' alias name>`

This simply passes the users' alias name to the door in order to be able
to present it to the user when entering mail in 'alias' areas.

An example would be: `ALIAS Rawhide`


### Identifier: `AREA <conference#> <settings>`

This presents a list of selected areas (or all areas optionally), and what
the flag settings for each area are. The `<conference#>` is the number of
the area that is selected and the `<settings>` contains the flags for that
area.

The possible flags are:

 Flag | Description
 -----|:------------------------------------------------------------
 a    | for all messages
 p    | for personal messages
 g    | for general messages (personal and those addressed to 'ALL')

In addition, some mail doors can add the following flags (this information
is set by the mail door itself, and overrides and standard BBS settings):

 Flag | Description
 -----|:------------------------------------------------------------
 w    | if this area should include mail written by themselves
 k    | if this area should be included in keyword searches
 f    | if this area should be included in filter exclude searches
 F    | if this area is forced to be read
 B    | if this area is blocked from getting replies

In further addition, area information should be given - this information
is gathered from the BBS records:

 Flag | Description
 -----|:------------------------------------------------------------
 P    | if the area is private mail only
 O    | if the area is public mail only
 X    | if either private or public mail is allowed
 R    | if the area is read-only (no posting at all allowed)
 Z    | if the area doesn't allow replies (no continuation of thread)
 L    | if the area is a local message area
 N    | if the area is a netmail area
 E    | if the area is an echomail area
 I    | if the area is an internet area
 U    | if the area is an newsgroup area
 H    | if the area is an handles only message area
 A    | if the area allows messages 'from' any name (pick-an-alias)
 &    | area allows file attaches (doesn't override CONTROLTYPE=ALLOWATTACH)

Again, the door should enforce security to messages with private flags
and/or different names to ensure that they are allowed.

For example, if a user had 4 areas selected, it might look like this:

    AREA 23 awOU
    AREA 31 aFPLA
    AREA 44 pkPN&
    AREA 172 gwkfPI

...please note that the flags ARE case sensitive, and any unknown flags
should be ignored.


### Identifier: `BULL <filename> <description>`

This is a way of describing the bulletins a little better than the file
name BLT-x.y method. The actual files should still be in the BLT-x.y
format to ensure backward compatibility, but by describing the filenames
here, you can have something like looks like:

    Bulletins
        - System Stats
        - Top Users
        - Contest galore!

Instead of:

    Bulletins
        - BLT-1.4
        - BLT-3.2
        - BLT-6.1

In this example, the TOREADER.EXT file would have three lines added to
create these descriptions:

    BULL BLT-1.4 System Stats
    BULL BLT-3.2 Top Users
    BULL BLT-6.1 Contest galore!


### Identifier: `ATTACH <filename> <conference#> <message#>`

This is a method used to identify which messages have files attached that
were downloaded with the packet. It is up to the mail door to decide
whether or not to send attached files; this is simply a method for the
reader to recognize such files.

For an example: Say that message number 782 in conference 12 had the file
TEST.ZIP attached to it, and the mail door included this TEST.ZIP file in
the BOARDID.QWK archive. The TOREADER.EXT file would have the follow entry
to let the mail reader know about this file:

    ATTACH TEST.ZIP 12 782


### Identifier: `FILE <filename> [description]`

This is a method used to identify which files in the QWK archive are file
requests from the bulletin board. It is up to the mail door to decide
whether or not to send these requested files, and this is simply a method
for the reader to recognize such files.

For an example: Say that the file GOODGAME.ARJ was requested and added to
the BOARDID.QWK archive. When the mail reader expanded it, it would not
know which files had been requested by the user, nor what the description
of those files were. Using the FILE identifier, the reader can easily
present a nice listing of requested files, rather than having to guess at
the file names, and having no idea of the description (although the
description is optional). In the GOODGAME.ARJ example, the mail door
should add the following line to the TOREADER.EXT file:

    FILE GOODGAME.ARJ This is a great new SVGA action game!

or

    FILE GOODGAME.ARJ


### Identifier: `KEYWORD <data>`, `FILTER <data>`, `TWIT <data>`

These identifiers are for mail doors that can process keywords, filters
and twit listings. It is used only to inform the mail reader of the
settings used, in order to make offline configuration much easier for the
user. For example, if the TOREADER.EXT file had the following lines...

    KEYWORD olms
    KEYWORD qwk
    FILTER hacking
    TWIT Death Wizard

...then the mail reader could present a list of these settings and allow
changes to be made to them, and create the proper TODOOR.EXT lines to
alter the configuration.

---


TODOOR.EXT
----------

This file is simply an ASCII text file that is included in the QWK packet,
and contains information used by the QWK mail door (created by the mail
reader). It must be read back and processed by the door.

Each line has an identifier, followed by the relevant information for the
extended information. (Arguments in `< >` are required, and ones in `[ ]`
are optional.)

    AREA      <conference#> <settings>
    RESET     <conference#> [#ofmessages]
    ATTACH    <filename> <reply#>
    FILE      <filename> [description]
    REQUEST   <filename>
    REQUEST   <conference#> <message#>
    KEYWORD   [-]<keyword>
    FILTER    [-]<filter>
    TWIT      [-]<twit>


### Identifier: `AREA <conference#> <settings>`

This presents a list of CHANGES areas, and what the flag settings for each
area changed are. The `<conference#>` is the number of the area that is to
be changed and the `<settings>` contains the flags for that area.

The possible flags are:

 Flag | Description
 -----|:------------------------------------------------------------
 D    | drop area
 a    | for all messages
 p    | for personal messages
 g    | for general messages (personal and those addressed to 'ALL')

In addition, some mail doors can add the following flags:

 Flag | Description
 -----|:------------------------------------------------------------
 w    | if this area should include mail written by themselves
 k    | if this area should be included in keyword searches
 f    | if this area should be included in filter exclude searches

For example, if a user had changed 2 areas, it might look like this:

    AREA 23 D
    AREA 44 gf


### Identifier: `RESET <conference#> [#ofmessages]`

This presents a list of pointer changes to be made. The pointers should be
changed for the `<conference#>` given. If the `[#ofmessages]` is blank
then the pointer should be set back to the start of the message base,
otherwise it should be set back `[#ofmessages]` back from the end of the
message base.

For example, if a user had modified 2 areas, it might look like this:

    RESET 23 100
    RESET 44


### Identifier: `ATTACH <filename> <reply#>`

This is a method of creating messages that have files attached to them.
The mail reader should pack the `<filename>` into the BOARDID.REP file,
and add this line in the TODOOR.EXT file, which is also included in the
BOARDID.REP file.

The `<reply#>` is the number of the message in the reply packet. For
example, if the third message in the reply packet had the file
ATTACHME.ZIP attached to it, the BOARDID.REP file should contain the file
ATTACHME.ZIP, BOARDID.MSG and the TODOOR.EXT file. The TODOOR.EXT file
would contain the line...

    ATTACH ATTACHME.ZIP 3

...within it. Note that it is up to the mail door whether or not it will
allow files to be attached to messages. This is governed by the mail door
inserting the line CONTROLTYPE=ALLOWATTACH in the DOOR.ID file.


### Identifier: `FILE <filename> [description]`

This is a method of uploading files in your mail packet. These are general
public files, and should be processed for credit on the bulletin board.

For example, if you included a file called GOODGAME.ZIP in your
BOARDID.REP file for credit on the BBS, the mail reader should insert the
line...

    FILE GOODGAME.ZIP This is a great new game!

... in the TODOOR.EXT file. It is up to the mail door whether or not it
accepts files to be uploaded with the mail packets. This can be detected
by the CONTROLTYPE=ALLOWFILES line being added to the DOOR.ID file in the
downloaded packet.


### Identifier: `REQUEST <filename>`, `REQUEST <conference#> <message#>`

These are file requests, either for files on the board, or files that are
attached to messages.

"REQUEST bob.zip", would request the file BOB.ZIP from the BBS's file
collection, whereas "REQUEST 12 333", would request the files attached to
message #333 in conference #12. It is up to the door to provide security
as to what can be requested.


### Identifier: `KEYWORD [-]<data>`, `FILTER [-]<data>`, `TWIT [-]<data>`

These identifiers are for changes made to the keywords, filters and twit
listings. It is used only to process CHANGES therefore if the setting
"KEYWORD bob" was given, it does not mean it is the only keyword in the
list, rather added to the list of keywords. To remove an entry, precede it
with a minus sign (-), so an example to remove a keyword "bob" from the
list of keywords would be "KEYWORD -bob"

---


Additional CONTROLTYPEs for DOOR.ID
-----------------------------------

 Command                       | Description
 :-----------------------------|:----------------------------------------
 `CONTROLTYPE=MAXKEYWORDS <#>` | max keywords the door can handle
 `CONTROLTYPE=MAXFILTERS <#>`  | max filters the door can handle
 `CONTROLTYPE=MAXTWITS <#>`    | max twits the door can handle
 `CONTROLTYPE=ALLOWATTACH`     | if the door allows file attachments
 `CONTROLTYPE=ALLOWFILES`      | if the door allows files to be uploaded
 `CONTROLTYPE=ALLOWREQUESTS`   | if the door allows files to be requested
 `CONTROLTYPE=MAXREQUESTS`     | max number of daily file requests

---


History
-------

 - Rev 1.02, 10/17/95 : Fixed a few typos
 - Rev 1.01, 12/04/94 : Added information on netmail and internet mail
 - Rev 1.00, 10/12/94 : Released official standard
