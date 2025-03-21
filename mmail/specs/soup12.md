Simple Offline USENET Packet Format (SOUP)
==========================================

Version 1.2  
Copyright (c) 1992-1993 Rhys Weatherley <rhys@cs.uq.oz.au>  
Last Update: 14 August 1993

Markdown by [William McBrine] on 17 January 2022, 19 March 2025


Distribution
------------

Permission to use, copy, and distribute this material for any purpose and
without fee is hereby granted, provided that the above copyright notice
and this permission notice appear in all copies, and that the name of Rhys
Weatherley not be used in advertising or publicity pertaining to this
material without specific, prior written permission. RHYS WEATHERLEY MAKES
NO REPRESENTATIONS ABOUT THE ACCURACY OR SUITABILITY OF THIS MATERIAL FOR
ANY PURPOSE. IT IS PROVIDED "AS IS", WITHOUT ANY EXPRESS OR IMPLIED
WARRANTIES.

NOTE: This document is NOT in the public domain. It is copyrighted.
However, the free distribution of this document is unlimited.

If you create a product which uses this packet format, it is suggested
that you include an UNMODIFIED copy of this document to inform your users
as to the packet format. All queries about this format, or requests for
the latest version should be directed to Rhys Weatherley at the above
e-mail address.


Introduction
------------

For many years, the FidoNet community has been using QWK and other formats
to enable users to download their mail and conferences to be read while
off-line. This not only saves phone charges and prevents tying up BBS
lines for long periods of time; it also allows a user to use much more
powerful tools on their own machine to process the downloaded "packets"
than what can be made available in an on-line environment.

To date however, very little work has been done in the USENET and dial-in
Unix community to facilitate the same user operations. Some attempts have
been made to use QWK, but due to QWK's limitations and unsuitability for
the USENET message formats, such efforts have not been very successful.

Within USENET, the tendency seems to be either "dial-in to some other
machine and put up with it", or "set up your own USENET site". The former
keeps the user at the mercy of whatever user interfaces the admin of the
other machine sees fit to install, and the latter requires far more
computing knowledge than the average computer user is expected to have.
Both of these can serve to lock out large portions of the computer-
literate public from experiencing USENET. The latter option can also give
rise to security problems in the form of forged USENET messages, which a
more controlled dial-in system avoids.

The purpose of this document is to define a new packet format which is
aware of the conventions used in the USENET community, forming a middle
ground between dial-in user interfaces and full USENET connectivity. It is
not limited to downloading USENET news however. The same format could be
used to enable a Unix user to package up their Unix mailbox and download
it for later perusal. The format is extensible to other kinds of news or
conference systems, so it is feasible, although not yet defined, that QWK
or FidoNet messages could be accomodated within the same packet as USENET
messages.


Revision history
----------------

__1.2__
: Add COMMANDS and ERRORS files. Renamed to "Simple Offline USENET
  Packet Format". A few extra fields and type codes for the AREAS and
  LIST files. Message area summaries.

__1.1__
: Add description of the LIST file. Everything else is identical to 1.0.

__1.0__
: Original version of the document.

Previously, this document was known as the "Helldiver Packet Format"
(HDPF). A variant of HDPF, called the "Simple Local News Packet format"
(SLNP) was created by Philippe Goujard (ppg@oasis.icl.co.uk). This
document combines the features of both previous formats and the name was
changed to make it less product-oriented.


Terminology
-----------

__Packet__
: a set of files, collected into a compressed archive.

__Message packet__
: the primary kind of packet which contains messages for the user to read.

__Reply packet__
: a special kind of packet which contains replies composed by the user,
  usually in response to the messages in a message packet.

__Packet generator__
: a program which generates packets to be downloaded and read, and which
  processes uploaded reply packets.

__Packet reader__
: a program which reads packets, usually by presenting the messages in a
  packet to the user, and which generates reply packets.

__Packet processor__
: either a packet generator or a packet reader.

__Generating host__
: the computer on which the packet generator executes.

__Reading host__
: the computer on which the packet reader executes.

__Download__
: the transfer of a packet from the generating host to the reading host.
  This transfer may take place in any fashion, although the most common
  method is through the use of a file transfer protocol such as Zmodem
  or Kermit.

__Upload__
: the transfer of a packet from the reading host to the generating host.

__Packet stream__
: a logical link between the generating and reading hosts over which
  downloads and uploads of packets take place.

__Message area__
: a collection of messages which are related by a common topic or
  purpose. Examples of message areas include USENET newsgroups, Unix
  mailboxes, and FidoNet conferences.

__Reply message area__
: a special kind of message area which contains replies being uploaded
  to a generating host.

__Text file__
: an ASCII file consisting of lines terminated by linefeed characters
  (LF, 10 decimal). Some operating systems terminate lines in a text
  file by CRLF pairs: such files must be converted to LF-terminated
  lines for transmission in a packet.


Anatomy of a packet
-------------------

A packet is a group of files, collected into a compressed archive. The
standard compression technique defined by this document is ZIP. Other
techniques such as ARJ, ZOO, ARC, LZH, etc can also be used. It is also
possible for Unix's tar.Z format to be used to transmit packets. The
minimum requirement is a method to collect a group of files into a single
packet, and a method to expand the packet back into the original files.
ZIP is specified to provide a common compression format for packet
processors. Each of the filenames in a packet should be stored in upper
case on those systems where case matters (e.g. Unix).

The following file specifications may appear in a packet:

 File       | Description
 :----------|:------------------------------------------------------
 `INFO`     | Optional textual information
 `LIST`     | List of message areas on the generating host
 `AREAS`    | Index of the message areas within the packet
 `REPLIES`  | Index of the reply message areas from the reading host
 `*.MSG`    | Text of the messages in a particular message area
 `*.IDX`    | Index information for messages in a message area
 `COMMANDS` | Extra commands sent along with a packet
 `ERRORS`   | Errors that occurred during the execution of commands

Other filenames may also appear in the packet, but are not defined by this
specification, so they should be avoided by generating software, and
ignored by receiving software.

The INFO file is an optional text file which may contain any kind of
textual information from the generating system. Typically this file would
only be present if there is some kind of urgent message that must be sent
to the receiving user. Use of this file to store the name of the
generating host and other such static information is possible, but
discouraged to save space and transmission time. If such information is
required, then the COMMANDS file can be used to transfer it.

The LIST file is an optional text file which contains a list of all
message areas that are available on the generating host, together with
the format of the messages. It is specified further in the section
"[LIST file]".

The AREAS file is a text file which contains an index of the message areas
present within the packet, specifying the name of the message area, the
filename the messages may be found in, and the message format. This is
specified further in the next section.

The REPLIES file is a text file which contains an index of the message
areas present within the packet that contain replies from the user which
should be mailed or posted on the generating host. In most cases, a packet
will contain either an AREAS file or a REPLIES file, but both may be
present. See the section "[REPLIES file]" below for more information.

The *.MSG files contain the text of the messages from a single message
area. The actual format of this file depends on the type of message area
specified in the AREAS file. See the section "[Message files]" below for
more information.

The *.IDX files provide an index into the *.MSG files, usually specifying
where each message starts and the contents of some of the common message
header fields. These files are intended for use by reading software on the
recipient's system to quickly display an overview of the messages present
in a message area. See the section "[Index files]" below for more
information.

The COMMANDS file is a text file which contains commands to be executed on
the reading or generating hosts to change the behaviour of the hosts at
each end of a packet stream. The ERRORS file contains textual error
messages to report to a human at the host the packet is destined for.
These two files are explained further in the section "[Sending commands
between systems]" below.


AREAS file
----------

The AREAS file is a text file containing zero or more lines, each of which
specifies a single message area, its encoding and the name of the
message/index file pair in which the messages appear. In particular, each
line has the following form:

    prefix<TAB>area name<TAB>encoding[<TAB>description[<TAB>number]]

where "prefix" specifies the name of the message/index file pair, "area
name" is the name of the message area, "encoding" specifies the formats of
the message and index files and the type of message area, "description" is
a descriptive name for the message area, and "number" is the number of
messages in the message file. The last two fields are optional. Additional
fields may be added in a future version of this specification.

The message and index files corresponding to the message area have the
names "prefix.MSG" and "prefix.IDX" respectively. If "prefix" contains
alphabetic characters, they must be upper case.

The message area name may be any sequence of printable ASCII characters
(space through tilde). Under USENET, this is typically a dotted name like
"comp.lang.c". Other networks may include spaces or other unusual
characters in the area names, so the receiving software must be aware of
this fact, and act accordingly. Also, receiving software must deal
gracefully with characters that have the high bit set, or names that
contain control characters, since people in other countries that speak a
language other than English may wish to use their country's native
encoding for the message area name. The only hard rule is that the name
may not contain TAB, CR or LF. Receiving software should treat the name as
an indivisible string to be displayed to the user.

The encoding field consists of two or three ASCII characters (usually
alphabetic). The first specifies the format of the message file, the
second specifies the format of the index file, and the optional third
specifies the kind of area (private or public). The following message file
formats are currently defined (case is significant):

 Format | Description
 -------|:-----------------------------------
 u      | USENET news articles
 m      | Unix mailbox articles
 M      | Mailbox articles in the MMDF format
 b      | Binary 8-bit clean mail format
 B      | Binary 8-bit clean news format
 i      | Index file only

The individual message file encodings are explained further in the next
section. The format 'i' indicates that no message file is present, and the
index file should be used as a summary of the messages in the message
area. This is explained further in the section "[Message area summaries]".
The following index file formats are currently defined (again, case is
significant):

 Format | Description
 -------|:--------------------------------------------
 n      | No index file
 c      | C-news overview database format
 C      | Shorter C-news overview database format
 i      | Offset/length pairs delineating the messages

These types are explained further in the section "[Index files]" below.

See the section "[Minimal conformance]" for information on the minimal
number of message and index formats that should be supported by packet
generators and packet readers.

The following kind of message areas are currently defined (again, case is
significant):

 Kind   | Description
 -------|:--------------------------------------------------
 m      | The message area contains private mail
 n      | The message area contains public messages, or news
 u      | The message area kind is unknown (the default)

This third letter is optional. If it is not present or unknown, the kind
of area depends on the message file type. Message types 'm', 'M', and 'b'
default to kind 'm', and message types 'u', 'B' and 'i' default to kind
'n'. It is not recommended that the value 'u' for this third letter be
used, although future versions of this specification may add additional
letters, necessitating 'u' to be placed in the third letter if the kind is
unknown. If the message area kind can be solely determined from the
message file type, it is recommended that the third letter be omitted to
save space and transmission time.

Further types may be defined in future versions of this specification. If
the packet processor does not recognise a message file type, it should
ignore the corresponding message and index files. If the packet processor
does not recognise a index file type, it can either ignore the message
file, or attempt to break down the message file into separate messages by
some other means. If the packet processor does not recognise a message
area kind, the kind should be treated as unknown. The user should be
warned if a message area has been ignored.

The optional message area description in the AREAS file consists of any
sequence of printable ASCII characters. This may be used to insert a
"readable" name for the message area. It may not contain TAB, CR or LF.

A message area may appear more than once in the AREAS file, each time with
a different prefix, but this is discouraged. This could be used to split
large message areas across more than one message file, but this is more
conveniently handled by generating a separate packet containing the area
contination.

The following examples demonstrate the capabilities of the AREAS file:

    0000000 Email       mn
    0000001 comp.lang.c uc        C Programming Language Discussions   125
    0000002 news.future Bc        Future of USENET                     38

    EMAIL   /usr/spool/mail/fred  unm   Private e-mail for fred
    U000001 comp.bbs.misc         MCn
    U000002 comp.bbs.waffle       ui


Message files
-------------

The format of the message file depends on the message file format
specified in the AREAS file. This version of the specification defines
three formats, which are in common use in the USENET and Unix community,
and two additional binary formats which permit messages to be stored with
no modification or assumptions about line lengths and byte values.

For each of these formats, lines are terminated with LF characters. Any CR
characters in the messages should be considered as data characters, or
ignored on receipt. In particular, MS-DOS systems should strip CR
characters from text messages before writing them to a packet.

A 'u' (USENET) message file is a text file consisting of one or more
messages prefixed with an rnews header. This header has the form "#! rnews
n" where "n" is the number of bytes in the message that follows the
header, excluding the line-feed character which terminates the header. If
the number in the header is followed by white space and other characters,
these other characters should be ignored, until the terminating LF
character is encountered.

A note about the rnews header: although a terser separator could be used,
the rnews header has the following advantages: (a) the messages can be
extracted in the absense of index files, or where the index files have an
unknown type, and (b) the message files can be imported into a USENET
system as standard rnews batches. Thus, if the user wishes to set up a
real USENET site, or simply use dedicated USENET software to read packets,
they can use their existing packet provider as a convenient read-only
newsfeed, with no extra burden placed on the system administrator of the
generating system.

A 'm' (Unix mailbox) message file is a text file consisting of one or more
messages. The first line of each message must start with the character
sequence "From ". Any remaining lines in the message which start with
"From " should have the character '>' prepended. Thus the "From " lines
delimit the message file into separate messages.

A 'M' (MMDF mailbox) message file is a sequence of one or more messages,
separated by at least 4 Control-A characters. The message file may
optionally start and end with a sequence of such characters. If a sequence
of 4 or more Control-A characters occurs in a message, it should be
"adjusted" by the insertion of spaces to split the sequence. The use of
Control-A characters within a message is discouraged.

The 'm' and 'M' formats were chosen for mail because of their common
occurrence in the Unix community. The generating system may elect to
instead convert a mailbox into the USENET format if it wishes, and set the
area kind to 'm' to inform the packet reader that the message area
contains private e-mail rather than news.

The 'b' (binary mail) and 'B' (binary news) formats are identical. The
contents of each message must conform to [RFC-822] / [RFC-1036] and may
contain content information compatible with [RFC-1341] (MIME). The only
difference between the messages of these formats and the preceding formats
is that no assumption is made about line lengths, and any of the 256
values for a byte may be used in any position. Each message is preceded by
a 4-byte value which indicates the length of the message in bytes, stored
in big-endian order (i.e. high byte first, low byte last). The difference
between 'b' and 'B' is a semantic one: message files of type 'b' are
expected to contain mail messages, and message files of type 'B' are
expected to contain news messages. Thus, reader software can make a
distinction between the two if it desires.

For most practical purposes, 'u', 'm' and 'M' should be sufficient. The
binary 'b' and 'B' types should be used for articles that contain 8-bit
binary data. It is possible to use type 'u' for binary data as well, but
'm' and 'M' cannot be because the message contents may be modified. When
MIME becomes more wide-spread, it is expected that binary messages
containing programs, sound, pictures and video will become popular,
necessitating these binary types.

Note that MIME messages can be stored in 'u', 'm' and 'M' message files,
but any binary components should be encoded with quoted-printable or
base64 (which is expected to be the most common usage of MIME in the near
future). It is not required that 'b' or 'B' be used for MIME messages:
only those containing raw unencoded binary data (as indicated by the
Content-transfer-encoding header value "binary").


Index files
-----------

This specification defines four index file types, which provide varying
degrees of support for packet readers.

Type 'n' indicates that no index file is present, and it is up to the
packet reader to extract messages from the message file. It is useful
where the generating system is providing a USENET newsfeed using packets,
and the receiving system is not interested in the index information.

A type 'c' index file is a text file (LF terminated lines), with one line
per message that occurs in the message file. The lines in the index file
should be in the same order as the corresponding messages. Each line has
the following form:

    offset<TAB>subject<TAB>author<TAB>date<TAB>mesgid<TAB>
    refs<TAB>bytes<TAB>lines[<TAB>selector]

(Note: the line-wrapping here is for document-formating purposes only. No
line-wrapping occurs in the index files). The fields have the following
semantics:

`offset`
: Seek position in the message file of where the corresponding message
  starts. The first seek position is 0. For the 'u' format, this
  indicates the start of the line following the rnews header line. For
  the 'm' format, this indicates the start of the "From " line and for
  the 'M' format, this indicates the start of the article after the
  Control-A sequence. For the 'b' and 'B' formats, this indicates the
  first byte of the message after the 4-byte message length.

`subject`
: The "Subject:" line from the message.

`author`
: The "From:" line from the message.

`date`
: The "Date:" line from the message.

`mesgid`
: The "Message-Id:" line from the message.

`refs`
: The "References:" line from the message.

`bytes`
: The number of bytes in the message. If this field is zero, then it
  indicates that there is no corresponding message in the message file.
  This is used for summaries: see the section "MESSAGE AREA SUMMARIES"
  for more details.

`lines`
: The "Lines:" line from the message. Note that this field is pretty
  useless these days on USENET, but is still popular. It is meant to
  indicate the number of lines in the body of the message. Generating
  software may elect to re-generate this value if it is not present in
  the original message, but this is not required.

`selector`
: A string used for summaries to request that a message be sent in a
  future packet. See the section "MESSAGE AREA SUMMARIES" for more
  details. This string will usually be a number, but other values such
  as Message-ID's could be used. Packet readers should treat this
  string as an indivisible string to be sent in a "sendme" command in
  the COMMANDS file. A zero-length string indicates that there is no
  selector string.

If any of these fields contained TAB's, newlines or other white space in
the original articles, they should be converted into single spaces. All
fields must be present, but some may be empty. The "bytes" field must not
be empty, since it provides necessary information for packet readers. Each
field must conform to the Internet RFC documents [RFC-822] or [RFC-1036].

Optionally, a header line may end with one or more extra TAB-separated
fields for other RFC-compliant header fields, together with the header
field names. e.g. `Supersedes: <1234@foovax>`. These fields are not
defined by this version of the specification, and are by arrangement
between the generating host and the reading host only.

This format is compatible with the news overview (NOV) database format of
C-news. The only difference being the substitution of an offset for the
article number used by C-news, and the addition of the "selector" field.
The C-news format was designed to assist threading newsreaders, so this
packet format should provide similar assistance to threading packet
readers.

The 'C' format is similar to 'c', except that the "mesgid" and "refs"
fields are dropped. These fields can commonly be quite long and are mainly
of use to packet readers which perform Message-ID based message threading.
Packet readers which perform subject threading (i.e. sort on the subject
line and then on the date and/or arrival order) do not require such
information. The format of the header lines in this case is as follows:

    offset<TAB>subject<TAB>author<TAB>date<TAB>bytes<TAB>lines[<TAB>selector]

Further TAB-separated fields may be added in future versions of this
specification.

The "author" field is slightly different to the 'c' format. Instead of an
[RFC-822] format address, it is just the author's name, extracted from the
"From:" line of the message. Most [RFC-822] and [RFC-1036] "From:" lines
have one of the following forms:

    address
    address (name)
    name <address>

Names may sometimes be surrounded by double-quote characters, have
embedded "(...)" sequences, or contain "useless" information after a comma
(",") or slash ("/"). The main requirement is that the generating software
produce some kind of (more or less) meaningful string for the name of the
author which can be displayed to the user by a packet reader. See
[RFC-822] and [RFC-1036] for more information on the syntax of the "From:"
line in messages.

The 'i' index format is purely binary, using 8 bytes for each message in
the corresponding message file. The first 4 bytes specify the offset into
the message file of the message and the remaining 4 bytes specify the
number of bytes in the message. Each 4-byte quantity is stored in
big-endian order (high byte first). This format is supplied to provide a
trade-off between transmission time and easy extraction of messages from a
message file.


REPLIES file
------------

One of the requirements for an off-line reading system is a mechanism for
a user to upload replies or new messages to a generating system for
mailing or posting. While it is possible to re-use the AREAS file for this
purpose, keeping the download and upload sections separate will help
prevent messages being fed back into a network erroneously.

The REPLIES file has a similar format to the AREAS file. Each line has the
following form:

    prefix<TAB>reply kind<TAB>encoding

The "prefix" and "encoding" fields are as before. The "reply kind" field
indicates the mechanism to use when transmitting the messages in the
message file. The following values are currently defined:

 Value  | Description
 -------|:-----------------------------------------------------
 mail   | Transmit an [RFC-822] compliant personal mail message
 news   | Transmit an [RFC-1036] compliant USENET news posting

On a Unix system, transmission of mail and news is usually performed with
the "sendmail" and "inews" programs respectively. Additional kinds may be
specified in a future version of this specification for other message
formats. Note: it is discouraged that the kinds "mail" and "news" be used
for anything other than RFC-compliant messages. In particular, FidoNet or
QWK messages should use a different reply kind. Messages of the same reply
kind can be placed in the same message file, or in separate message files.

Further TAB-separated fields may be added to the lines in the REPLIES file
in a future version of this specification.

It is recommended that a message file type of 'b' or 'B' be used for
sending replies to minimise the chance of message corruption. The
recommended index file types for replies are 'i' and 'n'. The index types
'c' and 'C' are discouraged because they do not provide useful information
for reply purposes.

The format of the messages in the message files should follow the relevant
RFC standards, with the following restriction: any "From:", "Sender:",
"Control:", "Approved:" or other similar "dangerous" header lines should
be ignored by the system transmitting the replies to prevent forgeries
from occuring. In particular, the "From:" header should be determined from
the user's login name, or some other similar means, rather than from any
data supplied in the user's message.

In most cases, mail messages will contain "To:", "Subject:", "Cc:", "Bcc:"
and "Reply-To:" header lines, and news messages will contain
"Newsgroups:", "Subject:", "Followup-To:", "Keywords:", "Summary:" and
"Reply-To:" header lines. Other optional headers (especially MIME content
headers) may also be present.

The automatic addition of a signature by the generating host which
receives the reply packet is discouraged. Signatures should be added by
the user's packet reading software instead, if desired.

A method for allowing replies from more than one person to be stored in
the same packet was considered, but was rejected for security reasons.

The following example demonstrates the capabilities of the REPLIES file:

    R001    mail    bn
    R002    mail    bi
    R003    news    Bn
    R004    news    Bi


LIST file
---------

The LIST file may be used to send a list of available message areas to the
receiving system. Its format is similar to the AREAS file, with the prefix
field deleted. Each line has the following form:

    area name<TAB>encoding[<TAB>description]

where "area name" is the name of the message area, "encoding" is a 2, 3 or
4 letter message, index, area kind, and subscription code, and
"description" is an optional message area description. Further optional
fields may be added in a future version of this specification.

The message, index, and area kind codes are the same as for the AREAS
file. The subscription code has one of the following values:

 Code   | Description
 -------|:-----------------------------------------------------
 y      | The user is subscribed to the message area
 n      | The user is not subscribed to the message area

If this field is not present, it defaults to 'n'.

Note that the message areas in the LIST file should only be those that can
be subscribed to or unsubscribed from using a request in the COMMANDS
file. Private e-mail message areas will normally not appear in the list.

The following example demonstrates the capabilities of the LIST file:

    alt.flame        ucnn
    comp.bbs.misc    ucny
    comp.bbs.waffle  ucny
    comp.lang.c      ucnn    C Programming Language Discussions
    news.future      ucny    Future of USENET


Sending commands between systems
--------------------------------

The COMMANDS and ERRORS files contain information for changing the
behaviour of each end of a packet stream, or for reporting errors in the
execution of commands or the generation of packets. Each is a text file
with LF-terminated lines.

The ERRORS file is the simplest: it consists of error messages from the
program which generated the packet to report on the progress of previously
executed commands. The format of these error messages is not defined, but
they should be human readable so that packet readers may present the
errors to the user for perusal.

The COMMANDS file consists of a sequence of commands, one per line, which
modify the behaviour of the packet processor at the other end of the
packet stream. Usually these commands are sent from the packet reader to
the packet generator to change the subscribed message areas, send files,
etc. The names of the commands are NOT case significant, but SHOULD be
sent in lower case. Any commands that are not understood by a program
should be ignored.

`version n.m`
: The command specifies the version of this specification that the
  packet conforms to. For this document the version is "1.2".

`date dd mmm ccyy hh:mm:ss [zone]`
: The date and time when the packet was created. To prevent confusion
  with different country's date formats, the date MUST always appear
  as "dd mmm ccyy". For example, "25 Jul 1993". This date format can
  be converted to local conventions if desired. "hh:mm:ss" is a
  24-hour clock time value. The "zone" field is the number of hours
  and minutes that the timezone is offset from Greenwich Mean Time as
  "+HHMM" or "-HHMM". For example, US Eastern Standard Time (EST) is
  "-0500", and Australian Eastern Standard Time is "+1000". If the
  zone is omitted, it defaults to "local time", however the zone should
  only be omitted if there is no way to determine it.

`subscribe name`
: This command requests the packet generating program to subscribe to
  a new message area. The area name may contain spaces, but not TABs.
  Additional fields may be added in a future version of this
  specification after a separating TAB. For now, ignore anything after
  a TAB. This command may generate an error message if the message area
  does not exist, or cannot be subscribed to.

`unsubscribe name`
: This command requests the packet generating program to unsubscribe
  from a message area. The same remarks about TABs and errors above
  also apply to this command.

`catchup [name]`
: This command requests the packet generating program to catchup on the
  nominated message area. That is, to mark all messages in the area as
  read and continue batching from the next message received. If the area
  name is not present, the packet generating program should catchup on
  all message areas.

`list [always|never]`
: This command requests the packet generating program to send a full
  list of all available message areas as a LIST file in the next packet.
  If the argument "always" is present, then the LIST file should be sent
  in every packet. The argument value "never" reverses this. For
  minimal compliance, "list always" should be treated as "list", and
  "list never" should be ignored.

`hostname string`
: This command specifies the name of the host or BBS the packet was
  generated on. It serves an informational role only. The string
  can be any sequence of printable ASCII characters.

`software string`
: This command specifies the name and version of the software which
  generated the packet. It servers an informational role only. The
  string can be any sequence of printable ASCII characters.

`sendme<TAB>area<TAB>selector[<TAB>selector[...]]`
: This command requests that the packet generator send a number of
  messages from the nominated message area. The "selector" arguments
  are taken from the "selector" fields in a 'c' or 'C' index file.
  Multiple "sendme" commands for the same message area may be present
  in a COMMANDS file. The maximum length for this command is 500
  characters. Note that other commands use spaces to separate
  arguments, but this command uses TAB's.

`mail y`
`mail n`
: This command changes whether or not private e-mail should be sent
  in generated packets.

`deletemail y`
`deletemail n`
: This command changes whether or not the user's private mailbox should
  be deleted after being batched into a packet.

`mailindex x`
: Set the preferred mail index format, where 'x' is one of the values
  'n', 'c', 'C' or 'i'.

`newsindex x`
: Set the preferred news index format, where 'x' is one of the values
  'n', 'c', 'C' or 'i'.

`get filename [putname]`
: Request that a file on the generating side be placed into a packet and
  sent to the packet reader. "putname" specifies the "filename"
  argument for the corresponding "put" command. If "putname" is not
  specified, the default is to use the base name of "filename". If
  directory paths are specified, the separator must be '/'. It should
  be noted that security could be breached through the use of this
  command, so programs which support this command should be very
  careful, preferably restricting requests to a particular directory
  tree.

`put pktname filename`
: This command is usually sent in response to a "get" command, although
  it can be sent on its own. "pktname" specifies the name of the file
  in the packet which contains the requested file's contents. The
  "filename" argument specifies destination file to write the contents
  to. Note that security could be breached with this command, so the
  destination filename should be checked, or restricted to a particular
  directory tree. It is also recommended that the user be prompted for
  confirmation before writing the file. If directory paths are
  specified in "filename", the separator must be '/'. It is recommended
  that the extension "FIL" be used for files in a packet which contain
  data sent with this command. For example, "put 001.FIL abc.zip"

`supported cmd ...`
: This command is usually sent from a packet generator to inform a
  packet reader as to which commands are supported by the generating
  program. The argument is a space-separated list of command names. For
  example, "supported subscribe unsubscribe list", or "supported
  subscribe unsubscribe catchup list mail deletemail".

It is recommended that at least "subscribe", "unsubscribe" and "list"
(with no arguments) be supported. Packet generators are recommended to add
a "supported" line to all packets generated to inform the packet reader
which commands can be used. In the absence of a "supported" line, only
"subscribe", "unsubscribe" and "list" should be assumed to be supported.

If more than one command is received for the same item (e.g.
"subscribe", "unsubscribe", "list", "mail", ...), then the last command
in the COMMANDS file takes precedence over any previous commands.

The following example demonstrates a typical COMMANDS file sent from a
packet generator:

    version 1.2
    date 25 Jul 1993 12:34:38 +1000
    hostname frobozz.domain.com
    software Fubar 1.3
    supported subscribe unsubscribe catchup list sendme get
    put 001.FIL abc.zip
    put 002.FIL def.txt

The following example demonstrates a typical COMMANDS file sent from a
packet reader:

    subscribe comp.lang.c
    subscribe comp.lang.misc
    unsubscribe alt.swedish.chef.bork.bork.bork
    list
    get xyzzy.zip
    get /usr/local/lib/fubar.txt frobozz.txt


Message area summaries
----------------------

The preceding sections have described a number of features for supporting
message area summaries. This section provides greater detail.

Since some message areas, notably USENET newsgroups, can get quite large,
the user may want to download a summary of a message area instead of all
of the messages, and then request that messages of interest be sent at
some later time for reading. Usually the summary will list the messages'
subjects, authors, and other similar "header information". Optionally, the
user may request that the first few lines of the messages also be sent so
that the user may peruse the beginning of the message and decide whether
to retrieve the rest of the message.

This activity is supported in the following fashion in this packet format:
summary information is sent in an index file of type 'c' or 'C', usually
with no accompanying message file. Therefore, the message file format in
the AREAS file will be set to 'i'. Each line in the index file has its
"bytes" field set to 0 to indicate that the message is not present in the
message file, and the "selector" field is set to some string that can be
used to request the message by way of a "sendme" command. Usually this
selection string will be the message number of the message on the
generating host, but other values such as Message-ID's are allowable.

If the first few lines of each message are also desired, the message file
format is set to something other than 'i', and the "offset" and "bytes"
fields in the index file may be used to extract the trimmed-down messages
for perusal. The "selector" field is once again used to request that an
entire message be sent at some later time, by way of a "sendme" command.

It is possible to create a message area which contains both ordinary
messages and summary messages. If the "selector" field is not present, or
is zero-length, then the message should be processed in the usual way, and
if the "selector" field is present and not zero-length, then it is a
summary message and the "bytes" field can be used to determine if the
first few lines of a message exist in the message file or not. This
mixture can be useful in some situations where the user wishes to download
all messages less than a certain length, and download the larger messages
as summaries, so that the larger messages can be explicitly requested only
if the user really wants them.


Minimal conformance
-------------------

This section describes the minimal amount of work that a packet processor
must do to be compliant with this specification.

Packet generators should be able to generate message areas for the 'b' and
'u' message formats for private and public message areas respectively, and
process replies for the 'b' and 'B' message formats. For minimal
conformance, index format 'n' must be supported, and if message area
summaries are required, one of index formats 'c' or 'C' should be
supported. It is recommended that either 'c' or 'C' be supported in all
packet generators, even when message summaries are not required. If
message summaries are supported, the minimal requirement is to send an
index file with the message file format set to 'i'. Packet generators
should support the "subscribe", "unsubscribe" and "list" commands, and
also the "sendme" command if message area summaries are required.

Packet readers should be able to read all message and index formats, and
generate replies for the 'b' and 'B' message formats. If message area
summaries are not supported, all areas with message format 'i' should be
flagged to the user as not understood. Packet readers should also be able
to display the INFO and LIST files if they are present in a packet and be
able to prompt the user for "subscribe" and "unsubscribe" requests to be
sent to the packet generator.


Future enhancements
-------------------

The obvious enhancement that can be made is to support other message
formats, especially FidoNet formats. Currently the message area file code
'q' is reserved for QWK-format messages. This will be defined in a future
version of this specification if demand warrants.

Experimentation with other formats and auxillary files is encouraged, but
please contact the author first to prevent double-ups from occurring. The
author may be contacted via e-mail at rhys@cs.uq.oz.au.


[AREAS file]: #areas-file
[Message files]: #message-files
[Index files]: #index-file
[REPLIES file]: #replies-file
[LIST file]: #list-file
[Sending commands between systems]: #sending-commands-between-systems
[Message area summaries]: #message-area-summaries
[Minimal conformance]: #minimal-conformance
[RFC-822]: https://datatracker.ietf.org/doc/html/rfc822
[RFC-1036]: https://datatracker.ietf.org/doc/html/rfc1036
[RFC-1341]: https://datatracker.ietf.org/doc/html/rfc1341
[William McBrine]: https://wmcbrine.com/
