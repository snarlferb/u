# universal text primer

- A universal place to store information associated w/ text processing: parsing, rendering and
  decoding text as well as user-created fonts that i will be working on (staccato12.bdf for bitmap
  and consolink.ttf for vector/resizeability) BDF fonts are often used in conjunction w/ formats
  such as PostScript or TrueType. My main reason for using BDF was because of its compatibility
  w/ X11, as the font format & metrics both align (you can find this out in the X Logical Font
  Description Conventions) You can also find Adobe's legacy doc/specifications...

  [Adobe Specification](https://adobe-type-tools.github.io/font-tech-notes)

  [X11 Specification](https://tronche.com/gui/x/xlib/graphics/font-metrics)

  I often go to all different websites to learn about a related filetype's format and structure,
  however you can visit apple's developer site to learn more about TrueType.

  [TrueType Reference](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM06/Chap6.html)

  You can find an overview of OpenType below. Because Windows does not support font families with
  more than the 4 basic styles (Regular, Italic, Bold, Bold Italic.) Font designers have to account
  for this by setting it up in a way such that all our fonts can be used in all operating systems.

  OpenType (a cross-platform font format) allows for this, complementing the Family and Style Name
  values with "Preferred Family Name" and "Preferred Style Name" values that will take precedence
  in software w/ OpenType acknowledgement.

  (I realize that this might appear like an exorbitant amount of information at first glance, however i just
  want it to be out there and accessible to eviry 1... i like elviry... ezmerelda, agetha, ant jemima...
  ... And Hooper-humper Dink?... I'll invite him too!!...)

  [OpenType Overview](https://learn.microsoft.com/en-us/typography/opentype)

  I have a separate markdown file for further explanation on the Unicode Standard, how it works,
  and everything about it.

  [Unicode Information](unicode.md)

  The following document presents the core conventions used within the FreeType library
  to manage font and glyph data.

  [Core Conventions](https://freetype.org/freetype2/docs/glyphs/index.html)

  And the following lists FAQs, API references, a step-by-step introduction into the
  FreeType library as well as an overview of some of its internals, and more.

  [FT Documentation](https://freetype.org/freetype2/docs/documentation.html)

  *Text proc, tutorial and API reference are useful for creating an application.*

  [Text Processing](https://freetype.org/freetype2/docs/glyphs/glyphs-5.html#section-1)

  [Tutorial](https://freetype.org/freetype2/docs/tutorial/index.html)

  [API Reference](https://freetype.org/freetype2/docs/reference/index.html)

  Aside from reading the following, quite possibly the best way to learn is to look at the source code or read the
  documentation of [FontForge](https://fontforge.org/en-US/documentation). If you ever were trying to learn all about
  digital images and font, FontForge is quite possibly the most comprehensive tool, and i really enjoy using the outline/
  spiro, and bitmap view to draw/edit w/. They did a good job making it feel smooth and intuitive. 

  If and when you learn how to draw w/ bezier curves, I found this page in *Technical References* that has some more advanced
  [terms/techniques](https://fontforge.org/docs/techref/stroke.html).

  We'll begin by going over some generic facts. Text is the actual content or string of characters that conveys information.
  It consists of letters, numbers, symbols, and whitespace that together form words, sentences, and paragraphs.

  A font is a specific style and size of a typeface="Times New Roman" for example, which is a design set of characters.
  A font determines how the text is visually represented. *In FreeType, a face object models a given typeface in a given style.*

  Font properties include things like weight and style. Font weight refers to the thickness of the strokes (which define outline
  fonts) that make up the letters in a font. Common weights include regular, bold, light, and black. Font style refers to the
  overall design of the font, such as whether it's italic, slanted, or upright. Fonts can also have decorations, they can be
  serifs or sans-serif (the presence or absence of decorative strokes at the end), they can be script (that resembles hand-
  writing or calligraphy) or perhaps even a Dingbat/wingding type of font, and we might call those font or type designs.

  The term "script" may also refer to a complete writing system, including its character set, letter forms, writing direction
  (left to right, right to left), and rules like capitalization, ligatures (connected letters), and diacritics (accents).
  Latin, Arabic, Cyrillic, and Devanagari are all examples of scripts.

  Of course, it might be casually spoken that a given font looks like a particular typeface, emphasizing its likeness to, e.g.
  "Oh my uncle Arnold said this font is similar to ‘Arial’." -And that's a perfectly valid way to give people some idea of what
  a font might resemble. Hey, you can compare it to the shape of a macaronia noodle in your pasta bowl, whatever works.

  I briefly want to gloss over some aspects of a Word/Processor as it might be relevant to the kinds of things we want to discuss
  or refer to later on. Line breaking is the process of determining where and how to break lines of text when they exceed the available
  width of a text container (such as a text box, window, or screen).

  In justified text, lines are broken so that both the left and right edges align with the container's margins. This can involve
  adjusting the spacing between words (called interword spacing) to fit the width. When text is word-wrapped, the line breaks occur
  at word boundaries. This maintains readability and avoids breaking words in the middle.

  Some text layout engines support automatic hyphenation, where words are hyphenated at appropriate points to improve line
  justification and avoid overly large gaps between words. Soft line breaks are explicitly inserted by the user or the software to
  indicate where a line should break, overriding automatic line breaking rules.

  Advanced text layout engines may adjust the positioning of characters at the edges of justified lines to create a visually aligned
  appearance.

  Text wrapping is closely related to line breaking but focuses on how text flows within a larger layout or container, such as a
  paragraph in a document or text in a web page. It ensures that text fits within the width of its container without exceeding it-which
  applies to paragraphs, columns, and other text containers in graphical interfaces.

  In dynamic layout systems, text wrapping adjusts automatically as the width of the container changes. This ensures that text remains
  readable and properly formatted across different screen sizes and orientations.

  Advanced text rendering systems can handle text wrapping within non-rectangular or irregularly shaped containers, such as text flow
  around images or within complex layouts.

  Now lets return to font and text specifications. The character set is just the set of characters in the font. The encoding is the way
  that those characters are ordered (or sometimes, the way the first 256 are ordered).

  Here *is* something weird that you may notice. If you do some font comparisons as i have, for example "gg sans" compared to "liberation",
  you'll realize that the sans fonts are weird because they look the same size as liberation at size 12, ergo 12pt in liberation is
  analogous to 16pt in a little sans font, i.e., gg sans (what discord and chatgpt are using on my system)

  Lookups are a broader concept in OpenType fonts, defining various character transformations. GPOS and GSUB are important tables within
  OpenType fonts that handle specific aspects of character positioning and substitution. cmap subtables are a specific type of subtable used
  within lookups for character-to-glyph mapping.

  CID fonts (Character ID) rely on `x` amount of cmap files, that which define encodings in a more general way. A cmap table translates
  character codes into corresponding glyph indices. This tells the font rendering engine which glyph to display for each character entered.
  
  A single font can have multiple cmap subtables, each supporting a different character encoding scheme (e.g., Unicode, Windows-1252).
  This allows a single font to work with various character sets used by different operating systems or applications.
  (see [./unicode](unicode.md) to learn more about encodings)

  Monospace fonts possess characters that occupy the same amount of horizontal space, regardless of its shape. Proportional font characters
  occupy varying amounts of horizontal space based on their shape and size, hence relative *proportion*.

  The size of text is usually given in points, rather than device-specific pixels. Points provide a standard unit for designers to specify the
  relative size they want for the text, independent of the device's pixel density. The resolution (dpi) tells us the pixel density of the device.
  Pixel density says how many pixels are crammed into an inch in that space, on that specific screen.

  By multiplying the point size by the resolution and dividing it by 72, we convert the desired relative size (points) into the pixel density
  needed to achieve a similar visual size on that particular device/screen with its specific pixel density (dpi).
  
  *In traditional typography a 10pt font was one where the block of metal for each glyph was 10 points high. The point size of a font is the
  unleaded baseline to baseline distance.*
  
  Pen position refers to the current point in the coordinate system where the next glyph will be drawn when rendering text. It's an analogy
  taken from traditional typesetting and calligraphy, where a pen or a type piece moves across a surface to place characters. *A type piece
  is a small physical block with an embossed character, which is irrelevant, unless you're starting a printing press*

  So (dpi) or dots per inch, refers to the resolution of a printed image, indicating the number of ink dots within a single inch; (ppi) or
  pixels per inch refers to the resolution of a digital image, and indicates how many pixels are packed into a single inch of the image.

  A bitmap format represents a glyph (visual image/character) as a grid of pixels, where each pixel has a specific color value (usually
  black-and-white or grayscale). It consists of a matrix of pixels (bitmap) that directly maps to the glyph's visual representation.
  
  Bitmap fonts are resolution-dependent. They are designed for specific sizes and resolutions, and scaling them up or down can lead to
  a loss of quality and pixelation.

  Bitmap glyphs can be rendered directly without additional processing, as they are already in a pixel format. You'll often see them
  used in low-resolution displays, fixed-size text, or when a particular pixel-perfect appearance is needed. BDF (Bitmap Distribution Format)

  BDF works on the encoding, not on GIDs (glyph IDs), so default characters are usually encoded. This means that the default character,
  often a blank space or undefined character, must have a valid encoding to ensure proper rendering and handling by applications.
  In some font formats, like TrueType or OpenType, Glyph IDs are used to reference specific glyphs.

  A glyph slot is a container where individual glyphs can be loaded, whether outline or bitmap format.
  
  Outline fonts are commonly used in scalable font formats. Fonts can also be stored in a font file as a series of vector shapes called outlines.
  Each outline is defined as a coordinate system (series of points) in what is called the master or EM space (will discuss that after).
  Points can be tagged to indicate that they are actually *on the curve*, or alternatively, conic or cubic bezier control points.

  In the latter case, they are called off-curve points; Therefore, on-curve points lie directly on the outline of the glyph and are used
  to define the main shape of the character.

  A path is a sequence of connected straight line segments and curves that define the outline of a letter or symbol in a font.
  These paths are created using mathematical functions, and Bezier curves are one of the most common types of functions used.
  Unlike raster images (made of pixels), Bezier curves are not affected by resolution changes. You can enlarge or shrink a font
  without losing quality, making them perfect for digital displays. Bezier curves define the smooth curves and lines that make
  up the outlines in a vector font.

  Outline fonts use a single stroke to define the shape of the character. The stroke is a continuous path that outlines the entire character.
  Stroked fonts use a series of strokes to define the shape of the character. The strokes are individual lines that are combined to form the character.
  
  The EM space is typically defined by an imaginary square called the EM square, which provides a framework for placing and scaling the font's glyphs.
  The size of this EM square is usually arbitrary during the design phase and is mainly used to ensure consistent proportions and relationships between
  different glyphs.

  The "EM size" or "units per EM (UPM)" is a specific numeric value that defines the resolution of the EM square in terms of font units, and each glyph
  is "fitted" to this space. Common values for UPM include 1000 up to 2048 units, and this determines how the glyphs will be scaled and rendered on
  different devices and at different sizes. The EM size is crucial for the font's rendering engine to map the designed glyphs to actual pixels or
  units on a display or printed page.

  Parsing vector and bitmap fonts follows a distinct process specific to the associated format used. When parsing a vector font such as TrueType
  or OpenType, the process involves deciphering structured data that defines each glyph using mathematical descriptions (Bézier curves).

  During the loading process, the parser reads through the font file's tables to extract information such as glyph outlines, hinting instructions,
  kerning pairs, and metadata related to font metrics and features. This parsed data enables the font renderer to scale glyphs smoothly to any size
  without loss of quality, by dynamically recalculating the curves and lines based on the output device's resolution.

  The scanline (separate but important process on the whole) and rasterization are such steps required for having some semblance of text processing
  in your application. Rasterization determines which pixels within a grid are covered by the outline of a glyph. Vector fonts are rasterized by
  converting those vector outlines (lines, shapes, etc.) into pixel data, wherein each point is mapped to the corresponding pixel on the screen.
  This involves transfering the bespoke outlines onto a pixel grid and filling the interiors of said shapes.

  Bitmap fonts are already rasterized in their current form, however they must still be parsed (and decoded into a useable form, specifically for
  FreeType's rendering pipeline)

  The pixel buffer (e.g., metrics-&gt;bitmap.buffer) acts as the primary storage for glyph bitmap data, organized row by row and holds pixel
  information in a format that can be manipulated further.

  Parsing a bitmap font (like those in BDF format) involves interpreting fixed-size grid representations of glyphs and corresponding metrics.
  The parser identifies and extracts bitmap data for each character, typically stored as sequences of hexadecimal values that encode pixel patterns
  (the hex and metadata that originated in the font file itself)

  When a function reads this data from the file, it converts each hexadecimal value into its corresponding binary representation. This conversion is
  essential because the application needs to manipulate and process the bitmap data in its memory space.

  Bitmap fonts store each glyph as a grid of pixels, where the number of bits used to represent each pixel (bits per pixel or "bpp") determines the
  range of colors or shades available, that is number of bits used to represent the color information of each pixel in a font character.
  Common depths include 1, 2, 4, 8, 16, and 32 bits per pixel. Higher bpp allows for more detailed or colorful glyphs.

  If the bitmap depth exceeds 8 bits per pixel (e.g., 16 or 32 bits), the function must ensure compatibility with the application's internal
  representation by discarding the lower-order bits after conversion. This process makes it so that only the significant bits (which represent the
  actual color or shade information) are used for rendering or other operations. Note, that bitmap font depth and display depth are two totally
  different things.

  After conversion, the binary pixel data is typically stored in a pixel buffer. The pixel buffer allocates memory to hold the bitmap data for each glyph.
  Depending on the bpp (bits per pixel) of the font, the pixel buffer organizes and manages the pixel data efficiently.

  RGB Packing (separate step) is a technique that uses allocated bits, and thus assumes a specific bpp allocation (likely 24-bit for RGB) and packs the
  individual red, green, and blue values (typically within the range of 0 to 255) into a single integer using bit shifting, typically.

  It ensures each color component occupies its designated bits within the total bpp. Therefore, by combining the individual red, green, and blue (RGB)
  components of a color into a single integer value for efficient storage and manipulation we can reduce the overall memory footprint and improve
  processing of said color data.

  So we know that bpp determines available color data; The number of bits per pixel dictates how many bits are allocated to represent each color channel
  (red, green, blue) within a pixel. For instance, with 8 bpp (8 bits per pixel), you might have 3 bits for each color channel, limiting the possible
  color combinations.

  *Ive gone this far into the actual application details, im wondering if i should mention bit blitting as well* Its definitely related, if you intend
  to look it up yourself, so go ahead.

  After conversion, the binary bitmap data for each glyph is stored in a data structure in memory. This structure typically includes metadata such as
  glyph metrics (width, height, bearing, advance) alongside the binary bitmap data. 

  After the font data is processed, rendering functions are used to display. These functions map characters to their corresponding glyph indices in the font.
  Once mapped, glyphs can be rendered at specific positions within a text layout. Functions like `advance_width` are often used to determine the spacing
  between glyphs for proper alignment.
  
  The outline is scaled from the world space (sometimes known as coordinate or master space) to the screen space using a simple scaling transform.
  This scaled outline can then be converted into a monochrome bitmap, or an anti-aliased one. However, such glyphs displayed on a low-resolution surface
  will often show numerous unpleasant artifacts. The following techniques address those challenges...

  Hinting refers to the process of adjusting the shapes of glyphs so that they align with the pixel grid of the screen. This adjustment is necessary because
  font outlines, which are defined in algorithms w/ mathematical curves, may not align perfectly w/ the discrete pixel grid of the output device, leading to suboptimal
  rendering especially at smaller sizes.

  Grid-Fitting is the general process of modifying glyph outlines in order to align some of their important features to the pixel grid in screen space.
  When done correctly, the quality of the final glyph bitmaps is massively improved.

  Grid-fitting thus improves the clarity of text by minimizing visual artifacts such as jagged edges or blurriness. It can be decomposed in two important phases...
  
  (1) Feature Detection: This pass is in charge of detecting important glyph and font features and produce control data ("hints") which describe them to the final renderer.

  (2) Alignment Control: This pass is in charge of scaling the outline, then applying the alignment operations described in the hints produced beforehand in order to grid-fit it.

  Traditionally, Feature Detection is performed when the font file is created. The hints produced can be stored in many different ways by what is called the hinter,
  which can be a program, a font designer or a combination of both (i.e. program computed hints + hand-edited ones).

  The final renderer performs the alignment control given the hints provided in the font file.

  The advantage is that the hints can be very accurate and describe very precisely the features and the way they should be grid-fitted. For example, the TrueType format
  simply creates a bytecoded program for each glyph outline that is interpreted on the final renderer. The bytecode used is a complete virtual processor, specialized in
  geometric operations.

  On the other hand, the Type 1 format uses much simpler and shorter hints, which forces the final renderer to interpret them more or less liberally.

  GASP (Grid-fitting And Scan-conversion Procedure) is part of the TrueType font format spec and is used to control the grid-fitting and anti-aliasing behavior
  of glyphs when rendered at different pixel sizes. It specifies whether anti-aliasing should be enabled or disabled for specific font sizes, ensuring optimal
  rendering quality across various resolutions and display devices. For example, it can define different rendering behaviors for small text sizes by disabling
  anti-aliasing to maintain sharpness, versus larger sizes by enabling anti-aliasing for smoother edges.

  Auto-fitting generally refers to the broader concept of adjusting font glyphs or text elements to fit within specified constraints or to optimize their appearance.
  This can include tasks such as adjusting spacing, scaling glyphs, or aligning them to a grid. While auto-fitting can encompass automatic hinting as one aspect
  (especially in the context of FreeType and similar libraries), it also includes other adjustments that optimize how text is displayed or processed.

  Automatic Hinting is a project that aims at the development of an automatic hinting module that would include both a feature-detection and alignment control pass.
  Its targetted for real-time performance; Its also intended to not rely on font-provided and format-specific hints that may already be present in the font files.
  This means that it must use algorithms that are both fast and accurate at all phases.
  
  Anti-aliasing is a technique used to smooth the edges of graphical elements, reducing jaggedness or aliasing artifacts caused by the discrete nature of digital displays.
  It works by blending colors along the edges of objects to create a smoother transition between foreground and background colors, resulting in a more visually pleasing
  appearance (especially for low-res)
  
  A good anti-aliasing scan-converter generates a gray-level image that computes the exact outline coverage on each pixel, or an approximation of it.
  These images have much more detail than a monochrome bitmap. Generally, many people argue that rendering anti-aliased scaled outlines is sufficient
  and that no additional grid-fitting is needed.

  However, experience proves that grid-fitting anti-aliased glyphs help a lot by enhancing the contrast of certain glyph features, mainly edges, in order
  to make them more readable. Most users have considered that non-hinted, anti-aliased outlines are fuzzy and painful to read.

  Subpixel Positioning refers to the ability to position graphical elements, such as text or images, at fractional pixel positions. By allowing elements
  to be placed between whole pixels, subpixel positioning can enhance the visual precision and smoothness of rendered content.

  Grid-Pixel Placement (26.6 Fixed-Point Format) refers to the "placement of a pixel on a grid" w/ fractional accuracy. Its used to determine the exact
  coordinates of where the glyph should be placed to achieve precise rendering. So to reiterate, 26.6 pertains to the fixed-point representation for pixel
  positioning (2^64 indicates the level of granularity within each of those positions)

  Kerning is the process of adjusting the spacing/relative positions between specific pairs of characters to improve the overall appearance and readability of text. 
  
  All glyphs have a width. This is the distance from the origin of the current glyph to the right edge of the glyph, and this width is also called the right side bearing.
  The horizontal distance between the origin and the leftmost edge of the glyph is called the left side bearing (it may be negative, positive or zero), the horizontal
  origin is where the glyph will start being drawn (will illustrate more below).

  The bounding box of a glyph is the smallest rectangle that can completely enclose the glyph (without touching). It is usually an array of [4] = ...
  - xMin: The leftmost point of the glyph (analogous to lbearing)
  - xMax: The rightmost point of the glyph (analogous to rbearing)
  - yMin: The bottom point of the glyph
  - yMax: The top point of the glyph

  Advance metrics define the spacing between glyphs when rendering text. These are primarily concerned w/ horizontal text layout:
  - Advance Width starts at the leftmost edge of the glyph's bounding box (as a reference point) to the next pen position.
    It ends at a calculated point that defines the total horizontal space the glyph consumes, (including its visual width) and any extra spacing.
  - Left Bearing (lbearing): The distance from the current pen position to the leftmost point of the glyph (xMin)
  - Right Bearing (rbearing): The distance from the current pen position to the rightmost point of the glyph (xMax)

  When dealing with proportional fonts where characters have varying widths, advanced width involves calculating this cumulative width of characters up to a certain point,
  where elements like cursor positioning, line breaking, and text wrapping function correctly.

  Scaled Width (swidth) represents the width of a glyph in font design units, which are typically defined by the font designer. Font design units are an abstract unit of
  measurement used internally by font files to represent the dimensions of glyphs. swidth is often scaled by a factor specified in the font file to convert it to pixel units
  for rendering at a particular size.

  swidth encompasses the horizontal advance width of a glyph. So, when we talk about the swidth, we're referring to both its intrinsic width as well as any additional space
  it needs for proper spacing in text layout.

  Device Width (dwidth) represents the horizontal advance width of a glyph in device pixels (pixels on screen) after any scaling or hinting has been applied.
  dwidth takes into account factors such as font size, hinting, and scaling. Ergo dwidth is related to the representation of a given width in pixels, where as
  swidth is related to the horizontal advance width of those bitmap pixels. In X11, height is calculated with "ascent + descent", where the origin of a proceeding character is [x + width, y]...

  When rendering text in X11, the next character's origin is typically positioned at the end of the current character's width along the x-axis and at the same y-coordinate.
  In other words, after rendering a character at position (x, y), the next character's rendering typically starts from the position (x + currentCharWidth, y).
  This ensures that characters are horizontally spaced correctly in the text layout.

  Please see the documentation/related passages to learn more about font metrics and other info.
