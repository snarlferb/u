# universal text primer

- A universal place to store information associated w/ text processing: parsing/rendering
  text as well as user-created fonts (staccato12.bdf, a bitmap distribution font format and
  consolink.ttf, truetype) BDF fonts are often used in conjunction with other font formats
  such as PostScript or TrueType. My main reason for using BDF was because of its
  compatibility w/ X11, as the font format & metrics both align (you can find this
  out in the X Logical Font Description Conventions) You can also find Adobe's legacy doc/specifications...

  [Adobe Specification](https://adobe-type-tools.github.io/font-tech-notes/)

  [X11 Specification](https://tronche.com/gui/x/xlib/graphics/font-metrics/)

  The following document presents the core conventions used within the FreeType library
  to manage font and glyph data.

  [Core Conventions](https://freetype.org/freetype2/docs/glyphs/index.html)

  And the following lists FAQs, API references, a step-by-step introduction into the
  FreeType library as well as an overview of some of its internals, and more.

  [Documentation](https://freetype.org/freetype2/docs/documentation.html)

  *Text proc, tutorial and API reference are useful for creating an application.*

  [Text Processing](https://freetype.org/freetype2/docs/glyphs/glyphs-5.html#section-1)

  [Tutorial](https://freetype.org/freetype2/docs/tutorial/index.html)

  [API Reference](https://freetype.org/freetype2/docs/reference/index.html)

  Lets go over some generic facts. Text is the actual content or string of characters that
  conveys information. It consists of letters, numbers, symbols, and whitespace that
  together form words, sentences, and paragraphs.

  A font is a specific style and size of a typeface, which is a design set of characters.
  A font determines how the text is visually represented. *In FreeType, a face object
  models a given typeface in a given style.*

  Font properties include things like weight and style. Font weight refers to the thickness of the
  strokes that make up the letters in a font. Common weights include regular, bold, light, and black.
  Font style refers to the overall design of the font, such as whether it's italic, slanted, or upright.

  The character set is just the set of characters in the font. The encoding is the way that
  those characters are ordered (or sometimes, the way the first 256 are ordered).

  CID fonts (Character ID) have no encodings. Instead they are designed to be associated
  with one or several cmap files which provide encodings in a general way. A cmap table
  translates character codes into corresponding glyph indices. This tells the font rendering
  engine which glyph to display for each character entered. A single font can have multiple
  cmap subtables, each supporting a different character encoding scheme (e.g., Unicode,
  Windows-1252). This allows a single font to work with various character sets used by
  different operating systems or applications. (see [./unicode.md](unicode.md) to learn more about encodings)

  Monospace fonts possess characters that occupy the same amount of horizontal space,
  regardless of its shape. Proportional font characters occupy varying amounts of
  horizontal space based on their shape and size, hence the relative proportion.

  The size of text is usually given in points, rather than device-specific pixels.
  Points provide a standard unit for designers to specify the relative size they want
  for the text, independent of the device's pixel density. The resolution (dpi) tells us
  the pixel density of the device. It captures how many pixels are crammed into an inch
  on that specific screen.

  By multiplying the point size by the resolution and dividing it by 72, we convert the
  desired relative size (points) into the pixel density needed to achieve a similar visual
  size on that particular device with its specific pixel density (dpi).

  A bitmap format represents a glyph (visual image/character) as a grid of pixels, where
  each pixel has a specific color value (usually black-and-white or grayscale). It consists
  of a matrix of pixels (bitmap) that directly maps to the glyph's visual representation.
  Bitmap fonts are resolution-dependent. They are designed for specific sizes and resolutions,
  and scaling them up or down can lead to a loss of quality and pixelation.

  Bitmap glyphs can be rendered directly without additional processing, as they are already
  in a pixel format. You'll often see them used in low-resolution displays, fixed-size text,
  or when a particular pixel-perfect appearance is needed. BDF (Bitmap Distribution Format)

  BDF works on the encoding, not on GIDs (glyph IDs), so default characters are usually encoded.
  This means that the default character, often a blank space or undefined character, must have\
  a valid encoding to ensure proper rendering and handling by applications. In some font formats,
  like TrueType or OpenType, Glyph IDs are used to reference specific glyphs.

  A glyph slot is a container where individual glyphs can be loaded, whether outline or bitmap format.
  
  Outline fonts are commonly used in scalable font formats. Fonts can also be stored in a
  font file as a series of vectorial shapes called outlines. Each outline is defined as a
  series of points in what is called the master or EM space. Points can be tagged to
  indicate that they are actually on the curve, or alternatively, conic or cubic
  bezier control points. In the latter case, they are called off points.

  In order to render a glyph to a given character size, the outline is scaled from the master
  space to the device space using a simple scaling transform. This scaled outline can then be
  converted into a monochrome bitmap, or an anti-aliased one. However, such glyphs displayed
  on a low-resolution surface will often show numerous unpleasant artifacts.

  Grid-Fitting is the general process of modifying glyph outlines in order to align some of
  their important features to the pixel grid in device space. When done correctly, the
  quality of the final glyph bitmaps is massively improved.

  Pen position refers to the current point in the coordinate system where the next glyph
  will be drawn when rendering text. It's an analogy taken from traditional typesetting and
  calligraphy, where a pen or a type piece moves across a surface to place characters.
  *A type piece is a small physical block with an embossed character if you didnt know.*

  All glyphs have a width (sometimes called an advance width), this is the distance from the origin
  of the current glyph to the right edge of the glyph, and this width is also called the right side
  bearing. The horizontal origin is where the glyph will start being drawn. The horizontal distance
  between the origin and the leftmost edge of the glyph is called the left side bearing (it may be
  negative, positive or zero).

  Hinting refers to the process of adjusting the shapes of glyphs so that they align with the pixel
  grid of the output device, typically a screen. This adjustment is necessary because font outlines,
  which are defined in algorithms w/ mathematical curves, may not align perfectly w/ the discrete
  pixel grid of the output device, leading to suboptimal rendering especially at smaller sizes.

  Grid-fitting is a process used in font rendering to adjust the outlines of glyphs so that they align
  neatly with the pixel grid. It improves the clarity of text by minimizing visual artifacts such as
  jagged edges or blurriness. It can be decomposed in two important phases...
  (1) Feature Detection: This pass is in charge of detecting important glyph and font
  features and produce control data ("hints") which describe them to the final renderer.

  (2) Alignment Control: This pass is in charge of scaling the outline, then applying the
  alignment operations described in the hints produced beforehand in order to grid-fit it.

  Traditionally, Feature Detection is performed when the font file is created. The hints
  produced can be stored in many different ways by what is called the hinter, which can be a
  program, a font designer or a combination of both (i.e. program computed hints + hand-edited ones).

  The final renderer performs the alignment control given the hints provided in the font file.

  The advantage is that the hints can be very accurate and describe very precisely the features
  and the way they should be grid-fitted. For example, the TrueType format simply creates a
  bytecoded program for each glyph outline that is interpreted on the final renderer.
  The bytecode used is a complete virtual processor, specialized in geometric operations.

  On the other hand, the Type 1 format uses much simpler and shorter hints, which forces the
  final renderer to interpret them more or less liberally.

  Automatic Hinting is a project that aims at the development of an automatic hinting module
  that would include both a feature-detection and alignment control pass. Its targetted for
  real-time performance; Its also intended to not rely on font-provided and format-specific
  hints that may already be present in the font files. This means that it must use algorithms
  that are both fast and accurate at all phases.
  
  Anti-aliasing is a technique used to smooth the edges of graphical elements, reducing jaggedness
  or aliasing artifacts caused by the discrete nature of digital displays. It works by blending
  colors along the edges of objects to create a smoother transition between foreground and background
  colors, resulting in a more visually pleasing appearance.
  
  A good anti-aliasing scan-converter generates a gray-level image that computes the exact outline
  coverage on each pixel, or an approximation of it. These images have much more detail than a
  monochrome bitmap. Generally, many people argue that rendering anti-aliased scaled outlines
  is sufficient and that no additional grid-fitting is needed.

  However, experience proves that grid-fitting anti-aliased glyphs helps a lot by enhancing the
  contrast of certain glyph features, mainly edges, in order to make them more readable.
  Indeed, most users consider than non-hinted, anti-aliased outlines are fuzzy and painful to read.
  Because anti-aliased outlines carry more detail, they need less adjustment to improve their quality.

  Subpixel Positioning refers to the ability to position graphical elements, such as text or images,
  at fractional pixel positions. By allowing elements to be placed between whole pixels, subpixel
  positioning can enhance the visual precision and smoothness of rendered content.

  Grid-Pixel Placement (26.6 Fixed-Point Format) refers to the "placement of a pixel on a grid" w/
  fractional accuracy. Its used to determine the exact coordinates of where the glyph should be
  placed to achieve precise rendering. So to reiterate, 26.6 pertains to the fixed-point represent-
  ation for positioning, while  2^64 indicates the level of granularity within each of those positions. 

  Kerning is the process of adjusting the spacing/relative positions between specific pairs of
  characters to improve the overall appearance and readability of text. 

  The bounding box of a glyph is the smallest rectangle that can completely enclose the glyph.
  It is usually an array of [4] = ...
  - xMin: The leftmost point of the glyph (lbearing)
  - xMax: The rightmost point of the glyph (rbearing)
  - yMin: The bottom point of the glyph
  - yMax: The top point of the glyph

  Advance metrics define the spacing between glyphs when rendering text. These are primarily concerned w/
  horizontal text layout:
  - Advance Width: The horizontal distance from the current pen position to the next pen position.
  - Left Bearing (lbearing): The distance from the current pen position to the leftmost point of the glyph (xMin)
  - Right Bearing (rbearing): The distance from the current pen position to the rightmost point of the glyph (xMax)

  Scaled Width (swidth) represents the width of a glyph in font design units, which are typically
  defined by the font designer. Font design units are an abstract unit of measurement used internally
  by font files to represent the dimensions of glyphs. swidth is often scaled by a factor specified
  in the font file to convert it to pixel units for rendering at a particular size.

  swidth encompasses the horizontal advance width of a glyph. So, when we talk about the swidth,
  we're referring to both its intrinsic width as well as any additional space it needs
  for proper spacing in text layout.

  Device Width (dwidth) represents the horizontal advance width of a glyph in device pixels (pixels on
  screen) after any scaling or hinting has been applied. dwidth takes into account factors such as font
  size, hinting, and scaling. Ergo dwidth is related to the representation of a given width in pixels,
  where as swidth is related to the horizontal advance width of those bitmap pixels. In X11, height is
  calculated with "ascent + descent", where the origin of a proceeding character is [x + width, y]...

  When rendering text in X11, the next character's origin is typically positioned at the end of the
  current character's width along the x-axis and at the same y-coordinate. In other words, after
  rendering a character at position (x, y), the next character's rendering typically starts from the
  position (x + currentCharWidth, y). This ensures that characters are horizontally spaced correctly
  in the text layout.

  Please see the documentation/related passages to learn more about font metrics and other info.
