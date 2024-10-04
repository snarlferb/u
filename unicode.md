### Unicode Hyperlinks

Unicode Character Database (UCD) is a collection of files that provide data and algorithms for handling
Unicode characters in software. It includes character properties, such as widths, classifications, and
other data needed for correct text processing. The Unicode Core Specification provides comprehensive
details about the Unicode Standard, which includes the encoding, representation, handling of text, etc.

[Unicode Character Database](https://www.unicode.org/ucd)
[Unicode Core Specification](https://www.unicode.org/versions/Unicode16.0.0/core-spec)

### Preface
  
Unicode is a universal character encoding standard that assigns a unique code point to almost every
character used in human languages.
It encompasses a vast range of characters, including letters, numbers, symbols, punctuation, and
special characters from various writing systems around the world.

The challenge lies in representing these Unicode code points as a sequence of bytes that computers can
understand. Different encoding schemes use varying approaches to achieve this.

It may be preferable to follow those encoding specified by the Unicode consortium, as there may exist
some slight differences between one vendor’s definition of an encoding and another. UTF-8 employs a
variable-length encoding scheme. This means it uses a different number of bytes (1 to 4) to represent a
character depending on its Unicode code point value.

Note: PostScript Unicode encoding does not quite mesh with the unicode standard. This is because
PostScript works w/ glyphs and Unicode deals with characters, therefore PostScript sees no distinction
between space and nobreak space.

Basic Latin characters (a-z, A-Z, 0-9, and common symbols) are encoded using a single byte (7 bits).
This makes UTF-8 efficient for representing commonly used characters in languages like English.

Characters outside the basic Latin set (e.g., accented characters, Cyrillic, Arabic): These require more
than one byte. UTF-8 uses a combination of multiple bytes (2, 3, or 4) to represent these characters.

The first 127 code points of Unicode are the same as ASCII. At the end we'll look further into UTF-8.

For text processing tasks, adhering to Unicode encodings recommended by the Unicode Consortium is crucial.
This ensures consistent character representation across different systems, avoiding potential issues that
might arise from those vendor-specific encoding variations. 

ISO (International Organization for Standardization) defines various character encodings, including
the ISO-8859 family for representing specific language characters. They are limited in scope compared
to Unicode. Unicode, on the other hand, is a broader character encoding standard that aims to represent
a much wider range of characters, including those from nearly all languages...

It doesn't have a single ISO number because it's a more comprehensive system encompassing various encodings.
Unicode provides a unique code point for each character, which enables consistent represntation.
Modern encodings like UTF-8 and UTF-16 are ways to represent these Unicode code points using a
sequence of bytes.

Canonical names or formal designations for character encodings are often defined by organizations like IANA
(Internet Assigned Numbers Authority); MIME (Multipurpose Internet Mail Extension) on the other hand is also
for standardizing names, but specifically in the context of internet communication protocols - as well as
for referring to character encodings. MIME itself doesn't create its own character encoding, as it acts
as a standardization layer on top of existing encodings like ISO-8859-1, Unicode (UTF-8, UTF-16, etc.)

UTF-8 (Unicode Transformation Format - 8-bit) is the most popular character encoding scheme for representing
Unicode characters. It's widely used for text data on the internet, in software applications, and various
digital communication channels due to its efficiency and versatility. UTF-8 is also backward compatible
with ASCII encoding.

ASCII (American Standard Code for Information Interchange) developed by the American Standards Association.
Which today we know better by ANSI (American National Standards Institute) Believe it or not the 8-bit standard
for a *Byte* was not established until 1993 w/ ISO/IEC 2382-1:1993. Prior to that a byte couldve been...
5, 6 or 7 bits. Crazy eh?... IEC is the International Electrotechnical Commission who have a joint
technical committee with the ISO.

A UTF-8 encoding is constructed following a specific pattern *(Im describing the bits within a single UTF-8 byte **unit**,
and NOT multiple bytes within an address... for this reason UTF-8 and other encodings-alike are independent of endianess):*

  _ _ _ _  _ _ _ _ (1 byte)

The leading byte has a specific pattern that tells us how many additional
continuation bytes follow, and how to extract the code point value from those bytes. Consequently there's a defined
scheme based on the leading byte's first few bits, and how the continuation bytes may follow. 
```
  First Byte MSB Pattern	          Number of Continuation Bytes     	Bits Used for Code Point in Leading Byte
  0xxxxxxx   (all zeros)                           0	                   Single-byte character (ASCII characters)
  110xxxxx   (followed by 6 zeros)	           0	                      Single-byte character (reserved for future use)
  110xxxxx   (followed by 1 zero ) 	           1	                      2-byte character
  1110xxxx   (followed by 2 zeros)	           2                    	3-byte character
  11110xxx   (followed by 3 zeros)	           3	                      4-byte character      (less common)
```
*Where it says e.g. "followed by n zeroes", that is denoting those zeroes that follow implicitly for the pattern, as opposed
to something we need to add literally for the pattern.*

    0 _ _ _  _ _ _ _

As you can see, if we encode one byte than we make the MSB (most significant bit) of whats considered the leading byte, zero, and place the
code point in the remaining seven bits, e.g. `110 0001` of the following...

    0 1 1 0  0 0 0 1

e.g. `a` is `U+61`, both (0110 0001) and (110 0001) is hexidecimal `61` (look it up so you can be sure)

As we've alluded to, once a code point is more than a single byte it needs to be encoded, hence the purpose of UTF-8, or
rather, the way in which we've decided to represent the code points using multiple bytes. Lets look at a new example:

    1 1 0 _  _ _ _ _    1 0 _ _  _ _ _ _

You place one's in the most significant part of the byte, followed by a zero (the remaining five bits are for the code point)
Our second byte has a similar rule; As the remaining six bits are for the code point.

    1 1 0 0  0 0 1 0    1 0 1 0  1 0 0 1

e.g. `©` (Copyright symbol) is `U+00A9`, and (11000010 10101001) is hexidecimal `C2 A9`

To summarize, single byte encodings always start with a zero, and multi-byte encodings always start w/ one.
The second bit of the additional bytes (........ 10xxxxxx 10xxxxxx 10xxxxxx) is always zero, this way you can always tell where a
code point starts and ends; Even when you start scanning somewhere in the middle of a given text.

The font being used sometimes such as on a webpage may not have a definition for a particular code point, and then cannot interpret it.

As a fallback, the browser shows a small square/rectangle called a missing character box to indicate that something is supposed to
be there but can't be displayed correctly. This often happens with symbols that aren't used frequently in a particular language
and might not be included in the default font. Even common fonts might not have every single symbol available.

So the Missing Character Box is a visual placeholder, but it shouldnt be confused with the Replacement Character which is a specific
character defined in the Unicode standard (`U+FFFD`) with a unique code point. It indicates an error or problem during data processing,
not just a missing font. The visual appearance of this character can vary depending on the system, but the black rhombus with a white
question mark is a common representation. It signifies that the system encountered some issue (corrupted data, incompatible encoding)
and couldn't interpret the data stream correctly.

Custom Glyph Lists

This is an interesting idea i read and borrowed from the FontForge documentation, which i thought would be appropriate to put here,
in case anyones interested... *Create a namelist.txt file (to list Unicode codepoints and glyph names)* For example:
```
0xEC00 octDotDhe
0xEC01 octDotDheDbl
0xEC02 octDotDheTrpl
0xEC03 octDotDheQdrpl
0xEC04 octDotLik
0xEC05 octDotLikDbl
0xEC06 octDotLikTrpl
0xEC07 minirLik
0xEC08 minirDhe
0xEC09 minirBawah
0xEC0A soroganDhe
0x-001 soroganLik
```
For glyphs without a Unicode point, use a codepoint of -1, such as in the last line of the above example.
Then load FontForge and go to (Encoding > Load NameList), and then use ‘Rename glyphs’, since ‘Load NameList’
only adds the custom namelist to the set of options available in subsequent rename commands.

