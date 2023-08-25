HME Protocol
============

From [HME SDK] 1.4.1  
Copyright © 2004-2006 [TiVo, Inc.]  All Rights Reserved  
Distributed under the [Common Public License]  
Markdown and tweaks by [William McBrine], August 2023


1 Protocol Overview
-------------------

![hme architecture]

The TiVo® HME protocol operates over a TCP/IP socket connection and is
bi-directional. The application sends commands to the TiVo DVR, and the
DVR sends events back to the application.

### 1.1 Protocol Handshake

Typically a device will initiate a connection after discovering an
application using Rendezvous. Once the connection is established the
application sends an 8-byte handshake message to the device:

Magic Number        | Reserved  | Major Version | Minor Version
------------------- | --------- | ------------- | -------------
0x53 0x42 0x54 0x56 | 0x00 0x00 | 0             | 44

When the device receives the handshake it decides whether it supports the
protocol version reported by the client. If it does, it sends its own
handshake message to the application using the same layout.

### 1.2 Chunked Encoding

At this point a connection is established. From now on the application can
send commands to the device, and the device will send events to the
application. However, after the handshake exchange the connections in both
directions switch to chunked encoding.

A chunked stream is a stream where each block of data is preceded by a
two-byte length. The chunk length is a 16-bit unsigned integer, which is
encoded using the most significant bits first (i.e. big-endian or network
order). A 0-length chunk, called the terminator ends each command or
event.

Here is an example of how a command or event sequence could be encoded:

Description | Length    | Data
----------- | --------- | -----------------
Chunk 1     | 0x00 0x08 | 8 bytes of data
Chunk 2     | 0x01 0x00 | 256 bytes of data
Terminator  | 0x00 0x00 | -
Chunk 1     | 0x00 0x12 | 18 bytes of data
Terminator  | 0x00 0x00 | -
...         |           |

Chunks can be between 1 and 65535 bytes long. A command or event can be
broken up into chunks of varying sizes. This is entirely up to the encoder
of the stream. The only requirement is that a 0-length chunk terminates
each command and event.

Chunked encoding is used so that it possible for the reader of the stream
to skip to the end of a command and/or event in case of an error of some
kind is encountered during the parsing of the event. In that case the
reader simply skips chunks until it reaches a terminator. At that point it
can continue interpreting the commands or events as before.

### 1.3 Startup Events

#### 1.3.1 Device Information

The device will send EVT_DEVICE_INFO to the application immediately
following a successful handshake. The event contains information about the
device that may be useful to applications.

#### 1.3.2 Resolution Information

After sending a device info event, the device will send an
EVT_RESOLUTION_INFO event to the application.  The event will contain the
current resolution, as well as a list of all available resolutions.  See
Section 4.8 for details on this event.  The initial current resolution for
the receiver will always be 640x480 PAR 1/1.  Typically, applications will
select their desired resolution via CMD_RECEIVER_SET_RESOLUTION just after
receiving this event and before creating any views.

#### 1.3.3 Init Information

After sending a resolution info event, the device will send an
EVT_INIT_INFO event to the application. The event may contain a memento
and arguments for the target application. See Section 4.7 for details on
this event.

#### 1.3.4 Application Information

Finally after the handshake, the device info event, the resolution info
event and the init info event have been sent, the device sends an
EVT_APP_INFO event telling the application that it is the active resource.

### 1.4 Data Types

The protocol uses simple on-the-wire data types. The following data types
are used:

Data Type | Bytes | Description
--------- | ----- | --------------------------------------------------
bool      | 1     | 0 = false, otherwise true
byte      | 1     | 8-bit signed integer
int       | 4     | 32-bit signed integer, most significant byte first
float     | 4     | 4-byte IEEE floating point number
vint      | 1..n  | variable length signed integer
vuint     | 1..n  | variable length unsigned integer
string    | 1..n  | UTF8 encoded string (see below)
vdata     | 1..n  | Variable length collection of bytes (see below)

### 1.5 Variable Length Integers

Variable length integers are encoded using one or more bytes. Each byte
contains 7 data bits. The 7th bit is set only in the last byte to mark the
end of the number. For signed integers, the 6th bit of the last byte is
the sign of the number, and so for signed integers the last byte therefore
only contains 6 data bits. For unsigned integers, the last byte contains 7
data bits.  Variable length integers are encoded so that the first byte
contains the least significant bits of the value.

This format can be used to encode values up to 64-bits in length. This
means the longest encoding for a variable length integer will be 10-bytes.

Variable length integers are used throughout the protocol because they are
good for encoding small integer values in a few bytes while still allowing
for larger values in exceptional cases.

### 1.6 UTF8 Encoded Strings

All strings are encoded using UTF8 encoding and are preceded by a vuint
length.  (Note that prior to version 0.40 of the protocol, the length of
strings were encoded as a 2 byte unsigned value rather than a vuint.
Receivers and applications wanting to be interoperable with peers using
earlier versions of the protocol must support both encodings.)

### 1.7 Variable Length Byte Collections

A variable length byte collection or vdata is headed by a vint indicating
the length and followed by the data itself.


2 HME Objects
-------------

Once a connection is established the application can start sending
commands to create its user interface. Commands operate on views and
resources. Views define the layout of the screen, while resources are
objects such as images and text.

### 2.1 Object Identifiers

In the protocol the application gets to assign an identifier to each view
and resource as it is created. This identifier is a number that can be
used to refer to the object later on. It is entirely up to the application
to allocate identifiers. Typically IDs are allocated in a monotonically
increasing sequence. But that is not a requirement. It is even possible to
reuse identifiers for new views or resources. This would make the object
previously identified by the ID inaccessible.

The following object identifiers are predefined and can be used
immediately:

ID                  | Value | Description
------------------- | ----- | ------------------------------------
ID_NULL             | 0     | empty resource
ID_ROOT_STREAM      | 1     | resource id of the application
ID_ROOT_VIEW        | 2     | root view of the application
ID_DEFAULT_TTF      | 10    | default true type font
ID_SYSTEM_TTF       | 11    | system true type font
ID_BONK_SOUND       | 20    | “bonk” sound
ID_UPDOWN_SOUND     | 21    | up/down arrow sound
ID_THUMBSUP_SOUND   | 22    | thumbs up sound
ID_THUMBSDOWN_SOUND | 23    | thumbs down sound
ID_SELECT_SOUND     | 24    | select sound
ID_TIVO_SOUND       | 25    | TiVo® sound
ID_LEFT_SOUND       | 26    | left arrow sound
ID_RIGHT_SOUND      | 27    | right arrow sound
ID_PAGEUP_SOUND     | 28    | page up sound
ID_PAGEDOWN_SOUND   | 29    | page down sound
ID_ALERT_SOUND      | 30    | alert sound
ID_DESELECT_SOUND   | 31    | deselect sound
ID_ERROR_SOUND      | 32    | error sound
ID_SLOWDOWN1_SOUND  | 33    | trickplay slow down sound
ID_SPEEDUP1_SOUND   | 34    | trickplay speedup 1 sound
ID_SPEEDUP2_SOUND   | 35    | trickplay speedup 2 sound
ID_SPEEDUP3_SOUND   | 36    | trickplay speedup 3 sound
ID_CLIENT           | 2048  | first ID that can be used by the app

### 2.2 Views

A user interface is built using a view hierarchy. Each view in this
hierarchy defines an area of the screen with its own coordinate system and
other attributes. A view can contain zero or more other views. Each view,
other than the root view, has a single parent view.

Views have the following attributes:

Attribute     | Type          | Description
------------- | ------------- | ---------------------------------------
id            | signed int 32 | view identifier
parent        | view          | parent view
children      | view[]        | zero or more child views
x, y          | signed int 32 | position in parent’s coordinate system
width, height | signed int 32 | view dimensions
tx, ty        | signed int 32 | translation of view’s coordinate system
sx, sy        | float         | scale of the view’s coordinate system
visible       | bool          | determines whether the view is visible
transparency  | float         | 0.0 – 1.0 (1.0 is entirely transparent)
resource      | resource      | resource displayed in the view

Views inherit the coordinate system of the parent view in which they are
embedded. The clipping rectangle is also inherited from the view’s
parents. A view can therefore never scribble outside its boundaries.

A view defines a coordinate system and does not display anything unless a
resource is assigned to it. When a resource is assigned to a view the
resource is displayed at location 0,0 in the view’s coordinate system.

If a view has a transparency greater than 0 it will affect the resource
displayed in the view as well as any children the view may have.

### 2.3 Resources

A resource is an object that can be displayed in a view. The following
resource types are defined:

Command             | Resource
------------------- | -----------------------------------
CMD_RSRC_ADD_COLOR  | color
CMD_RSRC_ADD_TTF    | TrueType® font
CMD_RSRC_ADD_FONT   | font
CMD_RSRC_ADD_TEXT   | UTF8 text
CMD_RSRC_ADD_IMAGE  | GIF, PNG, JPEG, or single frame MPG
CMD_RSRC_ADD_SOUND  | sound effect
CMD_RSRC_ADD_STREAM | streaming resource
CMD_RSRC_ADD_ANIM   | animation

Once a resource is created it can be assigned to multiple views. For
example, a background image can be used in many views without reloading it
again.

### 2.4 Events

Events are sent to applications in response to user input and status
changes. There are 4 types of event that are used:

Type            | Value | Description
--------------- | ----- | --------------------------------
EVT_DEVICE_INFO | 1     | device information
EVT_APP_INFO    | 2     | application status change
EVT_RSRC_INFO   | 3     | resource status change
EVT_KEY         | 4     | remote control event
EVT_IDLE        | 5     | receiver idle state change event
EVT_FONT_INFO   | 6     | font info event
EVT_INIT_INFO   | 7     | init info event

Key events are sent to the active resource. It is possible to make a
different resource active with CMD_RSRC_SET_ACTIVE. Some stream resources
are capable of handling some keys directly. For example, an audio stream
will correctly handle the pause key if it is made active.

Idle events are sent to the root application stream.

### 2.5 Painting

Normally all changes to the view hierarchy have an immediate effect on the
screen. This can cause undesired effects when a large number of changes
need to be made since some of the intermediate states will be visible
onscreen.

There are several ways this can be counteracted. First when creating a new
View hierarchy it is possible create the top-most view invisible. Once the
view hierarchy is created you can then make the top-most view visible in a
single operation.

The root view of an application is always created with visible set to
false. This allows the application to create its initial view hierarchy
and populate it with resources, before the root view is made visible.

When making lots of changes to a view hierarchy it is possible to turn off
painting of a parent view. This means that the initial state of the view
hierarchy will remain visible while the changes are made. The new changes
will only become visible when painting is turned back on.

### 2.6 Animation

Many operations in the HME protocol take an animation resource id
parameter. This means that the operation is not applied immediately.
Instead the change is applied over a period of time as described by the
animation resource.

An animation resource consists of a duration in milliseconds and an ease
in/out value. A negative ease (ease in) will gradually accelerate before
achieving a linear speed. A positive ease (ease out) will move linearly
and then decelerate. An ease of zero is entirely linear.

Type     | Value | Description
-------- | ----- | -------------------------------
ease in  | -1..0 | acceleration, then linear speed
linear   | 0     | linear speed
ease out | 0..1  | linear speed, then deceleration

For example, to fade in some text you can change the transparency of the
view from 1 (entirely transparent) to 0 (not transparent) over a period of
1000 milliseconds with an ease of zero. The effect will be that the view
fades in gradually over 1 second.

Smooth scrolling is achieved the same way. Rather than setting the
translation of a view to its new value immediately, you can use an
animation parameter to set the translation gradually. The result is a
smooth scrolling effect until the new translation is achieved.

### 2.7 Streams

It is possible to create a resource from a URL. In that case the resource
is streamed from the network to the device in a separate connection
initiated by the device. In that case the application will be notified of
the progress made during the download of the resource.

Streamed resources will also emit resource info events at regular
intervals as the data is played. This allows the application to display
progress information. A streaming resource can be created so that it plays
automatically as soon as it is ready, or it can be created so that it
starts of paused. In that case the application will receive a resource
info event indicating that the resource is paused, at which point the
application can start playback when it is ready.

### 2.8 Resolution

The video output format is always controlled by the HME Receiver
(typically in response to user configuration).  The format includes the
resolution, type of scan (interlaced or progressive), and potentially
other parameters beyond the scope of this document and HME.  The HME
application cannot change the video output format, but can request that
the receiver change the resolution at which HME applications are
composited into the output format.

All HME Receivers must support the 640x480 PAR 1/1 resolution (PAR = pixel
aspect ratio, describing the shape of an individual pixel on the screen),
even if this causes performance degradation due to a mismatch with the
current video output format.  Receivers may optionally support additional
resolution formats to provide better performance to applications that can
take advantage of resolution information.  Typically, when the HME
resolution format matches the video output format, the best possible
performance speed and quality is achieved.

Changing the resolution has no effect on the coordinate of any existing
views, which may cause the observed position of these views to shift on
screen.  It is up to the application developer to reposition views
appropriately during a resolution format change, typically by setting
painting false on the root view, rearranging the views (and possibly
replacing image resource assets with different resolution versions),
changing the resolution and setting painting true again.  However, note
that MPEG video background still images will scale according to the video
output format, and will always appear full screen.

Whenever the current resolution or the set of available resolutions
changes, an EVT_RESOLUTION_INFO event is sent to the application.   Only
the most recently received resolution info event should be used for future
CMD_RECEIVER_SET_RESOLUTION commands.


3 HME Commands
--------------

### 3.1 View Commands

This section describes the on-the-wire format for each of the HME commands
that operate on view objects.

#### 3.1.1 CMD_VIEW_ADD

This command creates a new view with the specified parent, and dimensions.
An ID for the new view must be provided by the application. The ID can
later be used to refer to this view again.

Field     | Type | Description
--------- | ---- | ---------------------------------
cmd       | vint | 1
id        | vint | ID of the new view
parent-id | vint | ID of the parent view, must exist
x         | vint | x position (horizontal)
y         | vint | y position (vertical)
w         | vint | width, must be >= 0
h         | vint | height, must be >= 0
visible   | bool | true if visible

The newly created view is created with no translation, scaling, or
transparency. To create top-level views the predefined ROOT_VIEW_ID must
be used as the parent-id.

#### 3.1.2 CMD_VIEW_SET_BOUNDS

Sets the bounds of the view to the newly specified values. All resources
and child views contained in this view will be clipped to the new
boundary.

Field     | Type | Description
--------- | ---- | --------------------------------------------
cmd       | vint | 2
id        | vint | ID of the view
x         | vint | x position
y         | vint | y position
w         | vint | width, must be >= 0
h         | vint | height, must be >= 0
animation | vint | ID of the animation or ID_NULL for immediate

If an animation is specified, the view’s boundary change is animated
according to the specified animation resource.

#### 3.1.3 CMD_VIEW_SET_SCALE

Changes the horizontal and/or vertical scale of the view. The scaling
factor may not be negative. The scale of a view affects all of its
content, including any resources and child views.

Field     | Type  | Description
--------- | ----- | ------------------------------
cmd       | vint  | 3
id        | vint  | ID of the view
sx        | float | horizontal scale, must be >= 0
sy        | float | vertical scale, must be >= 0
animation | vint  | ID of the animation or ID_NULL

If an animation is specified the view’s scale change is animated according
to the specified animation resource.

#### 3.1.4 CMD_VIEW_SET_TRANSLATION

Changes the horizontal and/or vertical translation of the view’s
coordinate system. The translation affects all of the view’s content,
including its children. Translation can be used to scroll the content of a
view.

Field     | Type | Description
--------- | ---- | ------------------------------
cmd       | vint | 4
id        | vint | ID of the view
tx        | vint | horizontal translation
ty        | vint | vertical translation
animation | vint | ID of the animation or ID_NULL

If an animation is specified, the view’s translation change is animated
according to the specified animation resource.

#### 3.1.5 CMD_VIEW_SET_TRANSPARENCY

Changes the transparency of a view. The transparency affects all of the
view’s content, including any child views. The default transparency of a
view is 0.0. If the transparency is set to 1.0, the view will be 100%
transparent and its content will not be visible.

Field        | Type  | Description
------------ | ----- | ------------------------------
cmd          | vint  | 5
id           | vint  | ID of the view
transparency | float | 0.0 = opaque, 1.0 = clear
animation    | vint  | ID of the animation or ID_NULL

If an animation is specified, the view’s transparency change is animated
according to the specified animation resource.

#### 3.1.6 CMD_VIEW_SET_VISIBLE

Set the visibility of a view. This affects all of the view’s content,
including any child views.

Field     | Type | Description
--------- | ---- | ------------------------------
cmd       | vint | 6
id        | vint | ID of the view
visible   | bool | true if visible
animation | vint | ID of the animation or ID_NULL

If an animation is specified, the view’s visibility change takes effect
only after the specified animation has completed.

#### 3.1.7 CMD_VIEW_SET_PAINTING

Set the painting parameter of a view. When a view’s painting parameter is
set to false its appearance on screen will not be changed, until painting
is turned back on. This feature is used to make sure that a series of
changes to a view hierarchy do not affect the display until all of the
changes are complete.

Field     | Type | Description
--------- | ---- | -------------------------
cmd       | vint | 7
id        | vint | ID of the view
painting  | bool | true means painting is on

#### 3.1.8 CMD_VIEW_SET_RESOURCE

Set the resource that will be displayed in the view. This may override a
resource previously assigned to the view. Note that resources may be
assigned to multiple views, but that views can only contain one resource
each.

Field     | Type | Description
--------- | ---- | -----------------------------
cmd       | vint | 8
id        | vint | ID of the view
resource  | vint | ID of the resource or ID_NULL
flags     | vint | see below

The following flags affect how the resource is displayed in the view:

Font Style         | Value  | Description
------------------ | ------ | -----------------------------------
RSRC_HALIGN_LEFT   | 0x0001 | horizontally left aligned
RSRC_HALIGN_CENTER | 0x0002 | horizontally centered (default)
RSRC_HALIGN_RIGHT  | 0x0004 | horizontally right aligned
RSRC_VALIGN_TOP    | 0x0010 | vertically top aligned
RSRC_VALIGN_CENTER | 0x0020 | vertically centered (default)
RSRC_VALIGN_BOTTOM | 0x0040 | vertically bottom aligned
RSRC_TEXT_WRAP     | 0x0100 | wrap long text strings
RSRC_IMAGE_HFIT    | 0x1000 | fit horizontally, keep aspect ratio
RSRC_IMAGE_VFIT    | 0x2000 | fit vertically, keep aspect ratio
RSRC_IMAGE_BESTFIT | 0x4000 | fit using the larger dimension

#### 3.1.9 CMD_VIEW_REMOVE

Remove a view. After this operation the ID of the view is no longer valid.
The view is removed from its parent; in addition all of its child views
are removed.

Field     | Type | Description
--------- | ---- | ------------------------------
cmd       | vint | 9
id        | vint | ID of the view
animation | vint | ID of the animation or ID_NULL

If an animation is specified, the view is removed after the specified
animation completes.

### 3.2 Resource Commands

This section describes the on-the-wire format for each of the HME commands
that operate on resources.

#### 3.2.1 CMD_RSRC_ADD_COLOR

Create a color resource. The color is specified in 32-bit ARGB format. The
alpha component is stored in bits 24-31, and 0xFF is opaque, 0x00 is
transparent.

Field | Type             | Description
----- | ---------------- | ----------------------
cmd   | vint             | 20
id    | vint             | ID of the new resource
color | 4 unsigned bytes | ARGB

When a color resource is assigned to a view. The entire visible area of
the view is filled with the color.

#### 3.2.2 CMD_RSRC_ADD_TTF

Add a TrueType Font resource. This command is used to download a new
TrueType font to the device. The length of the TTF data is implied by the
terminator in the underlying chunked-stream.

Field         | Type | Description
------------- | ---- | ----------------------
cmd           | vint | 21
id            | vint | ID of the new resource
TrueType-font | data | raw TrueType font data

A TTF resource cannot be assigned to a view.

#### 3.2.3 CMD_RSRC_ADD_FONT

Add a font resource to the system. A font is created from a TrueType-Font
given a size and style.

Field  | Type  | Description
------ | ----- | -------------------------
cmd    | vint  | 22
id     | vint  | ID of the new resource
ttf-id | vint  | TrueType font resource id
style  | vint  | see below
size   | float | point size

The following font styles are defined:

Font Style      | Value
--------------- | -----
FONT_PLAIN      | 0x00
FONT_BOLD       | 0x01
FONT_ITALIC     | 0x02
FONT_BOLDITALIC | 0x03

A font resource cannot be assigned to a view.

#### 3.2.4 CMD_RSRC_ADD_TEXT

Add a text resource. A text resource is constructed given a font resource,
a color, a text string, and some flags which control the text formatting.
The text may contain ‘\n’ characters, which force a line break.

Field   | Type   | Description
------- | ------ | -------------------------
cmd     | vint   | 23
id      | vint   | ID of the new resource
font-id | vint   | font resource id
color   | vint   | color resource id
text    | string | the text in UTF8 format

A text resource can be assigned to a view. It is displayed inside the
view’s boundary according to the specified alignment.

#### 3.2.5 CMD_RSRC_ADD_IMAGE

Create an image resource. The image data can be in JPEG or PNG format. The
length of the image data is implied by the terminated in the underlying
chunked input stream.

Field | Type | Description
----- | ---- | ----------------------------------
cmd   | vint | 24
id    | vint | ID of the new resource
image | data | GIF, JPEG, PNG or single frame MPG

An image resource can be assigned to a view.

#### 3.2.6 CMD_RSRC_ADD_SOUND

Create a sound resource that can be played for simple sound effect.
Currently only a single audio format is supported.

Field | Type | Description
----- | ---- | ---------------------------------------------
cmd   | vint | 25
id    | vint | ID of the new resource
sound | data | 8,000 Hz signed 16-bit little endian mono PCM

A sound resource cannot be assigned to a view.

#### 3.2.7 CMD_RSRC_ADD_STREAM

Create a resource from a URL. The device will connect to the URL and
download or stream the resource depending on its content type.

Field        | Type   | Description
------------ | ------ | -----------------------------------------
cmd          | vint   | 26
id           | vint   | ID of the new resource
url          | string | URL of the resource
content-type | string | content-type hint
play         | bool   | if true, the resource plays automatically

See Section 2.7 for more information on how streamed resources are
handled.

#### 3.2.8 CMD_RSRC_ADD_ANIM

Create an animation resource. Animation resources can be supplied as
parameters to some commands to animate the command instead of effecting
the change immediately.

Field    | Type  | Description
-------- | ----- | --------------------------------------------
cmd      | vint  | 27
id       | vint  | ID of the new resource
duration | vint  | duration of the animation in milliseconds
ease     | float | -1..0 = ease in, 0 = linear, 0..1 = ease out

See Section 2.6 for more information on animation resources.

#### 3.2.9 CMD_RSRC_SET_ACTIVE

Make a resource the active resource. Events are automatically routed to
the currently active resource.

Field  | Type | Description
------ | ---- | ------------------
cmd    | vint | 40
id     | vint | ID of the resource
active | bool | true if active

An application can deactivate itself by calling this method on its own
stream resource (ID_ROOT_STREAM). An application will be notified using an
application info event when it is activated or deactivated.

#### 3.2.10 CMD_RSRC_SET_POSITION

Sets the position at which the resource is played back. Resources which
are playing are immediately repositioned. Resources which are not yet
started will begin playing at this position.

Field    | Type | Description
-------- | ---- | ---------------------
cmd      | vint | 41
id       | vint | ID of the resource
position | vint | millisecs from origin

#### 3.2.11 CMD_RSRC_SET_SPEED

Sets the speed at which the resource is played back. Setting the speed of
a resource has effect for those streaming media types that support
multiple speeds. For example, MP3 streams can have a speed of 0 (paused),
or 1 (playing).

Field | Type  | Description
----- | ----- | --------------------
cmd   | vint  | 42
id    | vint  | ID of the resource
speed | float | 0 = paused, 1 = play

#### 3.2.12 CMD_RSRC_SEND_EVENT

Send an event to a resource.

Field      | Type | Description
---------- | ---- | ---------------------------------------------
cmd        | vint | 44
id         | vint | ID of the target resource, ID_NULL for parent
animation  | vint | ID of the animation, ID_NULL for immediate
event data | data | event data

An event can be send to the parent of the application by specifying
ID_NULL as the id. If an animation is specified, the event is delivered
when the animation has completed.

#### 3.2.13 CMD_RSRC_CLOSE

Close the stream associated with a streaming resource. This will stop
playing the resource immediately.

Field | Type | Description
----- | ---- | ------------------
cmd   | vint | 45
id    | vint | ID of the resource

#### 3.2.14 CMD_RSRC_REMOVE

Remove a resource. If it is a stream resource, the stream will stop
playing. The resource ID of this resource is no longer usable.

Field | Type | Description
----- | ---- | ------------------
cmd   | vint | 46
id    | vint | ID of the resource

### 3.3 Receiver Commands

This section describes the on-the-wire format for each of the HME commands
that operate on the receiver.

#### 3.3.1 CMD_RECEIVER_ACKNOWLEDGE_IDLE

Notifies the receiver that the application acknowledged the receipt of the
EVT_IDLE event.

Field   | Type | Description
------- | ---- | --------------
cmd     | vint | 60
id      | vint | ID_ROOT_STREAM
handled | bool | see below

"handled" indicates whether the application handled the idle event by
screensaving. If false, the receiver will take action to prevent burn-in.

The CMD_RECEIVER_ACKNOWLEDGE_IDLE message should only be sent to the
receiver in response to an EVT_IDLE message that indicates the receiver is
now idle.

#### 3.3.2 CMD_RECEIVER_TRANSITION

Requests a transition from the current application to the specified
destination. Data can be passed between the two applications via the
params argument. The application requesting the transition may wish to
retain some state, this can be done by passing data in the memento.

Field       | Type   | Description
----------- | ------ | -----------------------------------------
cmd         | vint   | 61
id          | vint   | ID_ROOT_STREAM
destination | string | URI to which to transition
type        | vint   | enum (see 3.3.2.1 Transition Types)
param       | dict   | parameters for the next (or previous) app
memento     | vdata  | app state to put on the navigation stack

If this command succeeds the current application will exit and the
application at the destination URI will be started with the given
parameters. If this command fails the receiver will send an app info event
(EVT_APP_INFO) indicating failure to transition. Below is a table of error
codes which will be included in the case of a transition failure:

Error Code                   | Condition
---------------------------- | -------------------------------------------
APP_ERROR_BAD_ARGUMENT       | Can't transition to the given URI.
APP_ERROR_BAD_COMMAND        | The receiver can't perform transitions.
APP_ERROR_INVALID_TRANSITION | From embedded app, or 'back' with no stack.

##### 3.3.2.1 Transition Types

There are three different types of transitions. The following table
outlines the types and what a device supporting transitions must do.

Transition Type     | Value | Effect
------------------- | ----- | ------------------------------------
TRANSITION_FORWARD  | 1     | forward, store app's url and memento
TRANSITION_BACK     | 2     | back to a previous application
TRANSITION_TELEPORT | 3     | teleport, empty the stack

When executing a transition forward the device pushes the transitioning
application's URL and provided memento onto a navigation stack for recall
later. It then starts the specified application with the provided
parameters sent in the init info event.

If the transition type was TRANSITION_BACK then the navigation stack is
popped and the transition is made to the URL that was on the top of the
stack. The init info sent to that application includes the parameters sent
in the transition command and it's memento (the one it provided when
transitioning forward) from the navigation stack. Note: it is not
meaningful to pass a destination or a memento when doing a transition
back. If a transition back is attempted when there is nothing on the stack
that transition may fail.

When a teleport transition is requested the navigation stack is emptied.
Then the device transitions to the specified destination URI passing the
provided parameters in the init info event. It is not meaningful to pass a
memento when requesting a teleport transition.

In all cases if the specified URI is not of a supported type then the
transition may fail.

#### 3.3.3 CMD_RECEIVER_SET_RESOLUTION

Sets the receiver resolution to the specified values.  In order to be
valid, the resolution must have been obtained from one of the valid
resolution formats provided in the most recently received
EVT_RESOLUTION_INFO events.  Requesting an unsupported resolution will
result in an EVT_APP_INFO error event.

Field           | Type | Description
--------------- | ---- | ------------------------------------
cmd             | vint | 62
id              | vint | ID_ROOT_STREAM
width           | vint | buffer width in pixels
height          | vint | buffer height in pixels
PAR numerator   | vint | pixel aspect ratio (PAR) numerator
PAR denominator | vint | pixel aspect ratio (PAR) denominator


4 HME Events
------------

### 4.1 EVT_DEVICE_INFO

A device info event is sent when the protocol handshake is complete.

Field | Type   | Description
----- | ------ | -------------------------
type  | vint   | 1
id    | vint   | ID_ROOT_STREAM
count | vint   | number of key value pairs
key   | string | first key
value | string | first value
...   |        |

This event contains a list of key value pairs that provide information
about the device. The following keys are defined:

Key      | Description
-------- | ------------------------
brand    | manufacturer brand
platform | underlying platform
version  | software release version

### 4.2 EVT_APP_INFO

An application info event is generated when the status of an application
changes. This event has the following layout:

Field | Type   | Description
----- | ------ | -------------------------
type  | vint   | 2
id    | vint   | ID_ROOT_STREAM
count | vint   | number of key value pairs
key   | string | first key
value | string | first value
...   |        |

This event contains a list of key value pairs that provide information
about the application. The following keys are defined:

Key        | Description
---------- | -------------------------
active     | whether the app is active
error.code | error code (see below)
error.text | error text

#### 4.2.1 Application Errors

When an error occurs on the device an application event is generated and a
warning code and text are include. The following warning codes are
defined:

Warning                      | Value | Description
---------------------------- | ----- | ------------------------------
APP_ERROR_UNKNOWN            | 0     | unknown error occured
APP_ERROR_BAD_ARGUMENT       | 1     | bad argument passed to command
APP_ERROR_BAD_COMMAND        | 2     | command not understood
APP_ERROR_RSRC_NOT_FOUND     | 3     | resource id was not found
APP_ERROR_VIEW_NOT_FOUND     | 4     | view id was not found
APP_ERROR_OUT_OF_MEMORY      | 5     | out of memory
APP_ERROR_INVALID_TRANSITION | 6     | invalid transition attempt
APP_ERROR_INVALID_RESOLUTION | 7     | invalid resolution request
APP_ERROR_OTHER              | 100   | consult error text for details

error.text contains the invalid resolution in the following string format:
`Resolution <width>x<height> PAR <numerator>/<denominator> invalid.`

### 4.3 EVT_RSRC_INFO

A resource info event is generated when the status of a resource changes.
This event has the following layout:

Field  | Type   | Description
------ | ------ | ---------------------------
type   | vint   | 3
id     | vint   | id of the resource
status | vint   | resource status (see below)
count  | vint   | number of key value pairs
key    | string | first key
value  | string | first value
...    |        |

This event contains a list of key value pairs that provide information
about the resource. The following keys are defined:

Key        | Description
---------- | ---------------------------------------------
error.code | error code (see below)
error.text | error text
speed      | current speed of the resource
pos        | DEPRECATED: `<position>/<duration>` of stream

In addition to the above keys, each media type may add keys that are
specific to the media type (see below).  The initial EVT_RSRC_INFO event
will contain all key/value pairs that are applicable to the resource.  In
general, a key/value pair is not sent in an EVT_RSRC_INFO message again
unless the value has changed from that previously sent.

The start and position are measured against the same origin.  For
resources that reference a position in absolute time (e.g. live tv
streams), the origin is the Unix Epoch (0:00:00 1/1/1970 GMT).  For other
streams (such as music clips), the origin is simply 0.

#### 4.3.1 Resource Status

Streaming resource automatically change their status based on the progress
of the download. Some resources are downloaded as a stream and can be
stopped and started. While others, such as images, are downloaded
completely before they can be used.

The following resource states are defined:

Status                 | Value | Description
---------------------- | ----- | ---------------------
RSRC_STATUS_UNKNOWN    | 0     | not initialized
RSRC_STATUS_CONNECTING | 1     | waiting to connect
RSRC_STATUS_CONNECTED  | 2     | a connection was made
RSRC_STATUS_LOADING    | 3     | loading first frame
RSRC_STATUS_READY      | 4     | first frame loaded
RSRC_STATUS_PLAYING    | 5     | media is playing
RSRC_STATUS_PAUSED     | 6     | media is paused
RSRC_STATUS_SEEKING    | 7     | media is seeking
RSRC_STATUS_CLOSED     | 8     | stream closed
RSRC_STATUS_COMPLETE   | 9     | download complete
RSRC_STATUS_ERROR      | 10    | some error occurred

Not all states apply to every resource type. For example, when downloading
a JPEG image, the media states will be CONNECTING, CONNECTED, LOADING,
COMPLETE. Resource info events will be generated at 1-second intervals
while downloading,

#### 4.3.2 Resource Errors

The following resource errors are defined:

Field                         | Value | Description
----------------------------- | ----- | ---------------------------------
RSRC_ERROR_UNKNOWN            | 0     | unknown error occurred
RSRC_ERROR_BAD_DATA           | 1     | data format error
RSRC_ERROR_BAD_MAGIC          | 2     | bad magic number for content-type
RSRC_ERROR_BAD_VERSION        | 3     | media version not supported
RSRC_ERROR_CONNECTION_LOST    | 4     | connection was lost unexpectedly
RSRC_ERROR_CONNECTION_TIMEOUT | 5     | connection timedout
RSRC_ERROR_CONNECT_FAILED     | 6     | connect failed
RSRC_ERROR_HOST_NOT_FOUND     | 7     | hostname was not found
RSRC_ERROR_INCOMPATIBLE       | 8     | incompatible media version
RSRC_ERROR_NOT_SUPPORTED      | 9     | media type not supported
RSRC_ERROR_BAD_ARGUMENT       | 20    | bad argument
RSRC_ERROR_BAD_STATE          | 21    | bad state

#### 4.3.3 Media type specific keys

The following keys are sent for specific media types.

##### 4.3.3.1 Video specific keys

The following keys are optionally sent for video resources in addition to
the common keys specified above:

Key         | Description
----------- | -----------------------------------------------------
framerate   | The number of frames of video per second in the media
channel     | A Trio Mind globally unique channel identifier
audiotrack  | The current audio track selected
audiotracks | Available audio tracks in a comma separated list

The format for an audio track is `<language> <modifier> <text>`, where:

- the language is specified using the ISO 639 language and ISO 3166
  country code (e.g. en_US)
- the modifier is one of 'mono' or 'stereo' (in the future additional
  modifiers may be defined, and should be ignored if not understood by the
  receiver)
- the remaining text (including spaces) is a non-localizable string
  suitable for display to the user (e.g. actor or director name, etc.)
- commas must be escaped with "\," and backslash must be escaped with "\\"

The audiotrack value is optional, but must be sent if a list of
audiotracks is sent; the list is optional, but must contain the current
audiotrack value if sent.

### 4.4 EVT_KEY

A key event has the following layout:

Field   | Type | Description
------- | ---- | ----------------------
type    | vint | 4
id      | vint | ID of the resource
action  | vint | key action (see below)
code    | vint | key code (see below)
rawcode | vint | raw IR code

#### 4.4.1 Key Actions

The following key actions are specified:

Action      | Type | Description
----------- | ---- | -------------------------------
KEY_PRESS   | 1    | key was pressed
KEY_REPEAT  | 2    | key is repeated (ie still down)
KEY_RELEASE | 3    | key was released

When a key is pressed on the remote control a key event is generated with
action set to KEY_PRESS. When the key is released an KEY_RELEASE key event
is sent. When the key is held down for a certain amount of time,
KEY_REPEAT events are generated at regular intervals.

#### 4.4.2 Key Codes

The following key codes are recognized:

Code            | Type     | Description
--------------- | -------- | ----------------------
KEY_UNKNOWN     | 0        | key not known
KEY_TIVO*       | 1        | TiVo® or equivalent
KEY_UP          | 2        | arrow up
KEY_DOWN        | 3        | arrow down
KEY_LEFT        | 4        | arrow left
KEY_RIGHT       | 5        | arrow right
KEY_SELECT      | 6        | select
KEY_PLAY        | 7        | play
KEY_PAUSE       | 8        | pause
KEY_SLOW        | 9        | play slowly
KEY_REVERSE     | 10       | reverse
KEY_FORWARD     | 11       | fast forward
KEY_REPLAY      | 12       | instant replay
KEY_ADVANCE     | 13       | advance to next marker
KEY_THUMBSUP    | 14       | thumbs up
KEY_THUMBSDOWN  | 15       | thumbs down
KEY_VOLUMEUP    | 16       | volume up
KEY_VOLUMEDOWN  | 17       | volume down
KEY_CHANNELUP   | 18       | channel up
KEY_CHANNELDOWN | 19       | channel down
KEY_MUTE        | 20       | mute
KEY_RECORD      | 21       | record
KEY_LIVETV*     | 23       | back to live TV
KEY_INFO        | 25       | info
KEY_DISPLAY     | KEY_INFO | display, same as info
KEY_CLEAR       | 28       | clear
KEY_ENTER       | 29       | enter
KEY_NUM0        | 40       | 0
KEY_NUM1        | 41       | 1
KEY_NUM2        | 42       | 2
KEY_NUM3        | 43       | 3
KEY_NUM4        | 44       | 4
KEY_NUM5        | 45       | 5
KEY_NUM6        | 46       | 6
KEY_NUM7        | 47       | 7
KEY_NUM8        | 48       | 8
KEY_NUM9        | 49       | 9

* Keys reserved for internal TiVo use.

#### 4.4.3 Optional Key Codes

The following key codes are recognized, but considered optional from the
standpoint of support by the protocol.

Code             | Type           | Description
---------------- | -------------- | ----------------------
KEY_OPT_WINDOW   | 22             | Window
KEY_OPT_PIP      | KEY_OPT_WINDOW | Picture in picture, same as window
KEY_OPT_ASPECT   | KEY_OPT_WINDOW | Aspect, same code as window
KEY_OPT_EXIT*    | 24             | exit
KEY_OPT_LIST*    | 26             | list now playing
KEY_OPT_GUIDE*   | 27             | guide
KEY_OPT_STOP     | 51             | stop
KEY_OPT_MENU     | 52             | dvd menu
KEY_OPT_TOP_MENU | 53             | dvd top menu
KEY_OPT_ANGLE    | 54             | angle
KEY_OPT_DVD*     | 55             | dvd
KEY_OPT_A        | 56             | A key or yellow triangle key
KEY_OPT_B        | 57             | B key or blue square key
KEY_OPT_C        | 58             | C key or red circle key
KEY_OPT_D        | 59             | D key or green diamond key
KEY_OPT_TV_POWER | 60             | TV power key
KEY_OPT_TV_INPUT | 61             | TV input key
KEY_OPT_VOD      | 62             | video on demand or VOD key
KEY_OPT_POWER    | 63             | power key for the set top box

* Keys reserved for internal TiVo use.

### 4.5 EVT_IDLE

The idle event is generated whenever the receiver enters or leaves the
idle state.

Field | Type | Description
----- | ---- | -------------------------------------
type  | vint | 5
id    | vint | ID_ROOT_STREAM
idle  | bool | true if the receiver is entering idle

Receivers may decide to become idle at any time, but generally after some
long pause in user input.  EVT_IDLE events that indicate the receiver is
entering the idle state should be acknowledged with an
CMD_RECEIVER_ACKNOWLEDGE_IDLE message.

### 4.6 EVT_FONT_INFO

A font info event is generated when a font resource is created.  It
describes the true type font metrics for the new font.

Field                | Type    | Description
-------------------- | ------- | -------------------------------------
type                 | vint    | 6
id                   | vint    | id of the font resource
ascent               | float   | ascent of the font above the baseline
descent              | float   | descent of the font (always positive)
height               | float   | height (distance between baselines)
line gap             | float   | gap between lines (AKA the leading)
metrics per glyph    | vint    | (currently 3)
glyph count          | vint    | number of glyphs having metrics
glyph id             | vint    | character id of a glyph
glyph advance width  | float   | pen advance width
glyph bounding width | float   | total width
`<unknown metric>`   | 4 bytes | as of yet undefined
...                  |         |

The glyph specific metrics are grouped consecutively in duples after the
global font metrics (ascent, descent, height and line gap).  The number of
metrics in each duple is specified by the 'metrics per glyph' field, and
the number off duples are specified by the 'glyph count' field.  All
metrics are 4 bytes, and unknown metrics (i.e. part of the duples beyond
that which the application understands) should be skipped to get to the
start of the next duple.  The currently defined glyph metrics are 'glyph
id', 'glyph advance width', and 'glyph bounding width'.

The advance width metric describes how far the pen should move from the
start of one glyph to the start of the next.  The bounding width metric
describes the total horizontal extent of the glyph, which in the case of
italics text may be greater than the advance width for the glyph.

### 4.7 EVT_INIT_INFO

An initialization info event which is generated when an application is
started. This event is intended to return navigation state information to
the application when it is started via a transition command.

Field   | Type  | Description
------- | ----- | ------------------------------------
type    | vint  | 7
id      | vint  | ID_ROOT_STREAM
params  | dict  | arguments for the application
memento | vdata | saved state for transitioning "back"

### 4.8 EVT_RESOLUTION_INFO

The RESOLUTION_INFO event specifies the current resolution of the
rendering buffer and includes a list of possible resolutions for the
buffer. The receiver sends a RESOLUTION_INFO event at application startup,
and each time the current resolution or the order or set of preferred
resolutions change (e.g. when the video output format of the receiver
changes).

The resolution formats listed in the 'available' n-tuple blocks are in
order of preference, from high to low, where higher preference typically
means that the available format will have better speed and visual
performance that lower preference entries.  Note that the current
resolution format is always also listed in the available formats, though
not necessarily with the highest preference.

Field                   | Type | Description
----------------------- | ---- | -----------------------------------
type                    | vint | 8
id                      | vint | ID_ROOT_STREAM
field count             | vint | fields per resolution (currently 4)
current width           | vint | buffer width in pixels
current height          | vint | buffer height in pixels
current PAR numerator   | vint | pixel aspect ratio numerator
current PAR denominator | vint | pixel aspect ratio denominator
...                     |      | extra resolution fields (vints)
resolution count        | vint | Number of available resolutions
available width         | vint | Available buffer width in pixels
available height        | vint | Available buffer height in pixels
available PAR numerator | vint | Available PAR numerator
available PAR denom.    | vint | Available PAR denominator
...                     |      | extra resolution fields (vints)
...                     |      | extra available resolution n-tuples


5 Limits
--------

### 5.1 Minimums

At a minimum, all HME devices can accept the following types of data.
Devices are not required to accept data that exceeds these limits.

Command                 | Minimum
----------------------- | -----------------------------
CMD_RSRC_ADD_FONT       | point size cannot exceed 256
CMD_RSRC_ADD_TTF        | fonts can be up to 1 MB
CMD_RSRC_ADD_TEXT       | text can be up to 16 KB
CMD_RSRC_ADD_IMAGE      | up to 512 KB, 1024x768 pixels
CMD_RSRC_ADD_SOUND      | sounds can be up to 128 KB
CMD_RSRC_ADD_STREAM     | URL can be up to 1 KB
CMD_RECEIVER_TRANSITION | URL can be up to 1 KB
CMD_RSRC_SEND_EVENT     | event can be up to 4 KB
CMD_RECEIVER_TRANSITION | memento cannot exceed 10KB


6 Miscellaneous
---------------

TiVo is the registered trademark of TiVo Inc.
TrueType is the registered trademark of Apple Computer, Inc.

All other trademarks are the properties of their respective owners.

[HME SDK]: https://tivohme.sourceforge.net/
[TiVo, Inc.]: https://www.tivo.com/
[Common Public License]: https://opensource.org/license/cpl1-0-txt/
[William McBrine]: https://wmcbrine.com/
[hme architecture]: hme-protocol.png
