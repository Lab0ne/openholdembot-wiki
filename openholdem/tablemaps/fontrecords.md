# Font Records

## Description 

Font records are used as lookup records for Regions that utilize Text
transformations. When a Text transformation is required for a region,
the foreground pixels in that region will be scanned left to right, and
the widest font record that matches those pixels will be considered a
match. The region will continue to be scanned left to right until all
the pixels in the region are consumed. The return value of that region’s
text transform is this entire sequence of ASCII characters.

## Manipulating font records using OpenScrape

![image](images/openholdem/tablemaps/os_editor_create_font.jpg)

The “Create Font” button on the Table Map Editor window is used to
create new font records. This button is only available if a Text
transform is specified for the selected region record.

To create new font records:

1.  Select a Region record that has a text transform

2.  Ensure that the Region’s color parameters provide good separation of
    foreground and background pixels (preview in the Pixel Separation
    field)

3.  Click the “Create Font” button

<div class="rem">

* 1*. The number of the text transform in the region (Text0, Text1,
Text2, or Text3) determines which Font record group (0 to 3) the new
fonts will be added to. Multiple font record groups may be necessary if
the casino uses multiple fonts or if fonts are blinking or otherwise
highlighted when it is a players turn.

</div>

Assuming that some of the pixels in the selected region represent
unknown font characters, then the following window will appear when the
“Create Font” button is clicked:

![image](images/openholdem/tablemaps/os_editor_add_font_record.jpg)

On this window, a list of the individual font characters that OpenScrape
thinks it has found is displayed in the list on the left side. As we
have not told OpenScrape what character each of these pixel groups
represent, they are displayed as question marks. (Note that the question
mark can be considered a valid font character as well, so if you use
this character in your Table Map, be aware of this fact).

For each unknown character in the list on the left, click to select it,
then either: Enter which character is represents in the “Character”
field (in this example, it is evident that the grouping of foreground
pixels represents the capital letter P), or Click on the “Delete” button
if this is not a character you want stored in the Table Map for some
reason, such as it is a duplicate, or the automatic character separation
didn’t work quite correctly

Clicking on the “Sort” button will arrange the characters alphabetically
in the list, which might make them easier to process.

When you dismiss this window by clicking on the “OK” button, OpenScrape
will add each of the characters in the list on the left, their ASCII
representation, and their pixel layout to the list of Table Map records.
This includes duplicates, and unknowns (question marks), so be sure to
remove these prior to clicking “OK”, or your Table Map might get filled
with superfluous Font records.

## Fuzzy fonts

“Fuzzy fonts” are sometimes used by casinos when displaying text on
their poker interfaces. You can identify if a casino uses fuzzy fonts by
looking closely at the pixel patterns for individual characters as they
are displayed at different times or in different locations on the
screen. (Hint: use zoom) As an example, if when you look at the capital
“A” character as it is displayed for seat 0’s name vs. seat 1’s name,
you can see that those two letters use different pixels, of different
colors, especially on the slanted arms of the “A”, you generally will
need to use fuzzy font recognition.

The general procedure for defining fuzzy font records is as follows:

1.  Decide which fonts will be fuzzy, and create those in a different
    Font group from your other fonts (we will use group 1 “Text1” in
    this example). Fonts can be created with the font color defined as
    the foreground pixel color, or the background color as the
    foreground pixel color. In the case of the latter, the so-called
    inverse fonting may sometimes give better results. Experiment! If
    using the font color for the foreground pixel color, it is probably
    best to make the fonts as thick as possible using a large RGB color
    cube radius. If using the background color as the foreground pixel
    color, a radius of zero probably works best.

2.  Create a t1type Symbol record (for the Text1 group), and set the
    free-form text to fuzzy for default tolerance (25%), or to a
    specific tolerance value (0.50 for 50%, 0.33 for 33%, for example).
    Experiment to find the best tolerance – all casinos are different in
    how they display fuzzy fonts. If this symbol is set to anything
    besides fuzzy or a floating point value, then the engine will used
    non-fuzzy font recognition engine.

3.  You can also create multiple samples for various characters, and it
    will help. The numbers “6”, “8”, and “3”, for example, are similar
    enough that distinguishing fuzzy versions of these is difficult.
    Creating more font records (samples) for the various 6’s, 8s and 3’s
    will help with the recognition.

## Technical Reference 

In the Table Map (.tm) file, these records will be preceded with the
characters t\$. Font records have the following format:

T\<n\>\$\<a\> \<x0\> \<x1\> \<x2\> ... \<x30\>

\<n\> is the font group (0-3) that this font record belongs to \<a\> is
an actual printable (non-whitespace) case-sensitive ASCII character
(note that the angle brackets \<\> are not used) \<x0\> to \<x30\> are
32bit hexadecimal values that describe the foreground pixels for the
character. The maximum individual character width is 31 pixels. These
values describe the on-off state of the pixels for the character, with 1
meaning on (foreground) and 0 meaning off (background).
