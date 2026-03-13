---
name: pdf-guru
description: PDF specification expert based on ISO 32000-1:2008 (PDF 1.7). Use when generating, parsing, debugging, or manipulating PDF files, working with PDF content streams, operators, font embedding, encryption, transparency, annotations, forms, export pipelines that produce PDF output, or any task requiring knowledge of the PDF file format internals.
user-invocable: false
---

# PDF Guru Skill — Complete PDF 32000-1:2008 (PDF 1.7) Specification Knowledge

> Comprehensive knowledge base compiled from the ISO 32000-1:2008 standard (756 pages).
> Source: https://opensource.adobe.com/dc-acrobat-sdk-docs/pdfstandards/PDF32000_2008.pdf

---

## 1. DOCUMENT OVERVIEW

PDF 32000-1:2008 is the first ISO edition of the Portable Document Format specification, published 2008-7-1. It covers PDF version 1.7 and is organized into 14 chapters plus 12 annexes. PDF is a file format for representing documents in a manner independent of the application software, hardware, and operating system used to create them.

---

## 2. PDF ARCHITECTURE — THE FOUR PILLARS

PDF syntax is built on four fundamental components:

**Objects** — A PDF is composed from a small set of basic data types: Booleans, Numeric (integer and real), Strings (literal in parentheses `()` and hexadecimal in angle brackets `<>`), Names (prefixed with `/`), Arrays (in square brackets `[]`), Dictionaries (in double angle brackets `<< >>`), Streams (dictionary + binary data between `stream`/`endstream`), and the Null object. Objects can be labeled with an object number and generation number (`7 0 obj ... endobj`) making them *indirect objects* referenceable as `7 0 R`.

**File Structure** — A conforming PDF has four parts: a one-line **header** (e.g. `%PDF-1.7`), a **body** containing all objects, a **cross-reference table** (xref) locating indirect objects by byte offset, and a **trailer** dictionary pointing to the xref and the document catalog via the `Root` entry. Incremental updates append new objects, xref sections, and trailers to the end of the file. PDF 1.5 introduced cross-reference streams as a compressed alternative to the traditional xref table.

**Document Structure** — The object hierarchy is rooted at the **catalog dictionary** (located from the trailer's `Root` entry). The catalog references the **page tree**, outlines, named destinations, article threads, and other document-wide structures. Each page is a **page dictionary** with entries for MediaBox, Contents (the content stream), Resources, Annotations, and more. The page tree is a balanced tree of intermediate nodes and leaf page objects.

**Content Streams** — A content stream is a sequence of operators and operands written in postfix notation that describes the visual appearance of a page. Content streams are distinct from the document structure and are associated with pages, form XObjects, patterns, and annotation appearances.

---

## 3. LEXICAL CONVENTIONS (§7.2)

The PDF character set divides into three classes: *regular*, *delimiter*, and *white-space*. White-space characters include NUL (0), HT (9), LF (10), FF (12), CR (13), and SP (32). Delimiters include `( ) < > [ ] { } / %`. Comments start with `%` and extend to end-of-line. The second line of a PDF file often has 4+ bytes with values ≥128 to signal binary content to transport software.

---

## 4. OBJECT TYPES IN DETAIL (§7.3)

**Boolean** — Keywords `true` and `false`.

**Numeric** — Integer objects (e.g., `123`, `+17`, `-98`, `0`) and real objects (e.g., `34.5`, `-3.62`, `+123.6`, `.002`). No PostScript-style radix or exponential notation is allowed.

**String** — Literal strings use parentheses with balanced nesting and backslash escapes (`\n`, `\r`, `\t`, `\\\\`, `\(`, `\)`, `\ddd` for octal). Hexadecimal strings use angle brackets (e.g., `<4E6F762073686F7274>`). In encrypted documents, all strings carry encrypted values.

**Name** — Introduced by `/` (e.g., `/Type`, `/Font`). From PDF 1.2, `#xx` hex encoding allows any byte in a name. Names are atomic symbols, not strings.

**Array** — Heterogeneous ordered collections in `[ ]`. May contain any object type, including nested arrays.

**Dictionary** — Associative tables of key-value pairs in `<< >>`. Keys must be name objects. The `/Type` entry conventionally identifies the dictionary type.

**Stream** — A dictionary followed by `stream`, binary data, and `endstream`. The dictionary must include `/Length` (byte count of data). Optional entries: `/Filter` (decoding filters), `/DecodeParms`, `/F` (external file), `/FFilter`, `/FDecodeParms`, `/DL` (decoded length).

**Indirect Object** — Any object labeled with `obj`/`endobj` markers and an object number + generation number pair, referenceable elsewhere as an indirect reference (`n g R`).

---

## 5. FILTERS (§7.4)

Stream data can be encoded/compressed using filters, which may be chained:

| Filter | Description |
|--------|-------------|
| **ASCIIHexDecode** | Decodes hex-encoded ASCII data |
| **ASCII85Decode** | More compact ASCII encoding (5 ASCII chars → 4 binary bytes) |
| **LZWDecode** | Lempel-Ziv-Welch compression (variable-length codes) |
| **FlateDecode** | zlib/deflate compression (most common) |
| **RunLengthDecode** | Simple run-length encoding |
| **CCITTFaxDecode** | CCITT Group 3/4 fax compression for bilevel images |
| **JBIG2Decode** | JBIG2 compression for bilevel images (PDF 1.4+), 20:1–50:1 on text |
| **DCTDecode** | JPEG (lossy DCT-based) |
| **JPXDecode** | JPEG 2000 (PDF 1.5+) |
| **Crypt** | Encryption/decryption filter for selective stream encryption |

---

## 6. FILE STRUCTURE DETAILS (§7.5)

**Header** — First line `%PDF-1.x` where x indicates the minor version. The `Version` entry in the catalog dictionary can override for versions ≥1.4.

**Cross-Reference Table** — Traditional format starts with `xref` keyword, followed by subsections. Each entry is 20 bytes: 10-digit byte offset, 5-digit generation number, `n` (in-use) or `f` (free). PDF 1.5 introduced **cross-reference streams** — stream objects that encode xref data more compactly using the `/W` array to specify field widths.

**Trailer** — Contains `/Size` (total object count), `/Root` (catalog reference), `/Encrypt` (encryption dictionary), `/Info` (document information dictionary), `/ID` (two-element array of file identifiers), `/Prev` (offset of previous xref section for incremental updates).

**Incremental Updates** — New body objects, xref section, and trailer are appended. Previous data remains intact, enabling undo and efficient saving. The `/Prev` entry in each trailer links to the preceding xref.

**Linearized PDF** (Annex F) — A special file organization that enables efficient incremental access over network connections (byte-serving). The first page is placed near the beginning of the file, and a hint table enables random page access without downloading the entire file.

### Minimal PDF File Structure Example

```
%PDF-1.7
%âãÏÓ

1 0 obj
<< /Type /Catalog /Pages 2 0 R >>
endobj

2 0 obj
<< /Type /Pages /Kids [3 0 R] /Count 1 >>
endobj

3 0 obj
<< /Type /Page /Parent 2 0 R /MediaBox [0 0 612 792]
   /Contents 4 0 R /Resources << /Font << /F1 5 0 R >> >> >>
endobj

4 0 obj
<< /Length 44 >>
stream
BT
/F1 24 Tf
100 700 Td
(Hello World) Tj
ET
endstream
endobj

5 0 obj
<< /Type /Font /Subtype /Type1 /BaseFont /Helvetica >>
endobj

xref
0 6
0000000000 65535 f
0000000015 00000 n
0000000068 00000 n
0000000125 00000 n
0000000295 00000 n
0000000393 00000 n

trailer
<< /Size 6 /Root 1 0 R >>
startxref
472
%%EOF
```

---

## 7. ENCRYPTION & SECURITY (§7.6)

PDF encryption protects strings and streams. Integers, booleans, and names remain unencrypted (structural information stays accessible). The **encryption dictionary** in the trailer contains: `/Filter` (security handler name), `/SubFilter` (interoperable format), `/V` (algorithm version 1–5), `/Length` (key length in bits).

**Standard Security Handler** — Supports user password (to open) and owner password (to set permissions). Permissions control printing, modification, text extraction, annotation editing, form filling, and accessibility extraction.

| V Value | Algorithm | Key Length |
|---------|-----------|------------|
| 1 | RC4 | 40-bit |
| 2 | RC4 | Variable (40–128 bit) |
| 4 | AES-128 or RC4-128 | 128-bit (with crypt filters) |
| 5 | AES-256 | 256-bit |

**Public-Key Security** — Uses PKCS#7 certificates for encryption, enabling document sharing with specific recipients.

---

## 8. DOCUMENT STRUCTURE (§7.7)

**Catalog Dictionary** — The root object. Key entries:

| Entry | Description |
|-------|-------------|
| `/Type` | Always `/Catalog` |
| `/Pages` | Page tree root |
| `/PageLabels` | Page numbering labels |
| `/Names` | Name dictionary tree |
| `/Dests` | Named destinations |
| `/ViewerPreferences` | Viewer display settings |
| `/PageLayout` | SinglePage, OneColumn, TwoColumnLeft, etc. |
| `/PageMode` | UseNone, UseOutlines, UseThumbs, FullScreen, UseOC, UseAttachments |
| `/Outlines` | Bookmark tree root |
| `/OpenAction` | Action on document open |
| `/AcroForm` | Interactive form dictionary |
| `/Metadata` | XMP metadata stream |
| `/StructTreeRoot` | Structure tree for tagged PDF |
| `/MarkInfo` | Marked content info |
| `/Lang` | Natural language identifier |
| `/OCProperties` | Optional content (layers) configuration |

**Page Tree** — Balanced tree structure with intermediate nodes (`/Type /Pages`, `/Kids`, `/Count`) and leaf page nodes (`/Type /Page`). Inheritable entries (MediaBox, Resources, CropBox, Rotate) propagate down the tree.

**Page Dictionary** — Key entries:

| Entry | Description |
|-------|-------------|
| `/MediaBox` | Physical page size (required) |
| `/CropBox` | Visible region (default = MediaBox) |
| `/BleedBox` | Bleed area for production |
| `/TrimBox` | Intended finished page size |
| `/ArtBox` | Meaningful content extent |
| `/Contents` | Content stream(s) |
| `/Resources` | Resource dictionary |
| `/Rotate` | Page rotation (0/90/180/270) |
| `/Annots` | Annotation array |
| `/Thumb` | Thumbnail image |
| `/Trans` | Page transition effects |

**Document Information Dictionary** — Metadata entries: `/Title`, `/Author`, `/Subject`, `/Keywords`, `/Creator`, `/Producer`, `/CreationDate`, `/ModDate`, `/Trapped`.

---

## 9. CONTENT STREAMS & RESOURCES (§7.8)

Content streams use postfix (RPN) notation where operands precede operators. The **resource dictionary** maps resource names used in the content stream to actual objects.

| Resource Category | Description |
|-------------------|-------------|
| `/ExtGState` | Graphics state parameter dictionaries |
| `/ColorSpace` | Color space definitions |
| `/Pattern` | Pattern objects |
| `/Shading` | Shading dictionaries |
| `/XObject` | Form XObjects and images |
| `/Font` | Font dictionaries |
| `/ProcSet` | Procedure sets (legacy) |
| `/Properties` | Marked-content properties |

---

## 10. GRAPHICS MODEL (§8)

### Graphics Objects

Five categories: path objects (lines, Bézier curves, rectangles), text objects, external objects (images, forms), inline images, and shading objects.

### Coordinate Systems

| Space | Description |
|-------|-------------|
| **Device space** | Hardware pixels on output device |
| **User space** | Default content stream coordinates (1 unit = 1/72 inch) |
| **Text space** | For text positioning |
| **Glyph space** | Font glyph definitions |
| **Image space** | Image sample coordinates |
| **Form space** | Form XObject coordinates |
| **Pattern space** | Pattern cell coordinates |

Transformations use 3×2 **transformation matrices** `[a b c d e f]` supporting translation, scaling, rotation, skewing, and reflection. The **CTM** (Current Transformation Matrix) maps user space to device space.

### Graphics State Parameters (Table 52)

| Parameter | Type | Description |
|-----------|------|-------------|
| CTM | array | Current transformation matrix |
| clipping path | (internal) | Boundary for cropping output |
| color space | name/array | Current color space (stroking & non-stroking) |
| color | (various) | Current color value |
| text state | (various) | Nine text-specific parameters |
| line width | number | Stroke thickness (default: 1.0) |
| line cap | integer | 0=butt, 1=round, 2=projecting square |
| line join | integer | 0=miter, 1=round, 2=bevel |
| miter limit | number | Maximum miter length ratio |
| dash pattern | array+number | Dash array and phase |
| rendering intent | name | Color mapping intent |
| blend mode | name/array | Transparency blend mode |
| soft mask | dictionary/name | Soft mask for transparency |
| alpha constant | number | Constant opacity (CA for stroking, ca for non-stroking) |

### Path Construction Operators

| Operator | Operands | Description |
|----------|----------|-------------|
| `m` | x y | Move to point (begin new subpath) |
| `l` | x y | Line to point |
| `c` | x1 y1 x2 y2 x3 y3 | Cubic Bézier curve (two control points) |
| `v` | x2 y2 x3 y3 | Bézier with current point as first control |
| `y` | x1 y1 x3 y3 | Bézier with endpoint as second control |
| `h` | — | Close subpath (line back to start) |
| `re` | x y w h | Rectangle |

### Path Painting Operators

| Operator | Description |
|----------|-------------|
| `S` | Stroke the path |
| `s` | Close and stroke |
| `f` / `F` | Fill using non-zero winding rule |
| `f*` | Fill using even-odd rule |
| `B` | Fill then stroke (non-zero) |
| `B*` | Fill then stroke (even-odd) |
| `b` | Close, fill, stroke (non-zero) |
| `b*` | Close, fill, stroke (even-odd) |
| `n` | End path (no-op, used with clipping) |
| `W` | Set clipping path (non-zero winding) |
| `W*` | Set clipping path (even-odd) |

### Colour Spaces

| Category | Spaces |
|----------|--------|
| **Device** | DeviceGray, DeviceRGB, DeviceCMYK |
| **CIE-based** | CalGray, CalRGB, Lab, ICCBased |
| **Special** | Indexed, Pattern, Separation, DeviceN, NChannel |

### Color Operators

| Operator | Description |
|----------|-------------|
| `g` / `G` | Set gray (non-stroking / stroking) |
| `rg` / `RG` | Set RGB color |
| `k` / `K` | Set CMYK color |
| `cs` / `CS` | Set color space |
| `sc` / `SC` / `scn` / `SCN` | Set color in current space |

### Shading Types

| Type | Description |
|------|-------------|
| 1 | Function-based |
| 2 | Axial (linear gradient) |
| 3 | Radial (circular gradient) |
| 4 | Free-form Gouraud-shaded triangle mesh |
| 5 | Lattice-form Gouraud-shaded triangle mesh |
| 6 | Coons patch mesh |
| 7 | Tensor-product patch mesh |

### Images

Image XObjects are streams with entries: `/Width`, `/Height`, `/ColorSpace`, `/BitsPerComponent`, `/Filter`, `/Decode`, `/ImageMask`, `/Mask`, `/SMask` (soft mask for transparency), `/Intent`, `/Interpolate`.

Inline images use `BI`/`ID`/`EI` operators for small images embedded directly in the content stream.

### Form XObjects

Reusable content streams with their own Resources, BBox, and optional Matrix. Referenced via the `Do` operator. Used for page templates, annotation appearances, transparency groups.

### Optional Content (§8.11)

Layers that can be toggled on/off. Organized into Optional Content Groups (OCGs) controlled by the document's `/OCProperties`.

---

## 11. TEXT (§9)

### Text State Parameters

| Operator | Parameter | Description |
|----------|-----------|-------------|
| `Tc` | Character spacing | Extra space between characters |
| `Tw` | Word spacing | Extra space at each space character |
| `Tz` | Horizontal scaling | Percentage (100 = normal) |
| `TL` | Leading | Vertical distance between lines |
| `Tf` | Font & size | Select font and point size |
| `Tr` | Rendering mode | 0=fill, 1=stroke, 2=fill+stroke, 3=invisible, 4–7=with clipping |
| `Ts` | Text rise | Baseline offset (for superscript/subscript) |

### Text Object Operators

| Operator | Description |
|----------|-------------|
| `BT` / `ET` | Begin / end text object |
| `Td` | Move to next line (relative offset) |
| `TD` | Move and set leading |
| `Tm` | Set text matrix (absolute positioning) |
| `T*` | Move to start of next line |
| `Tj` | Show text string |
| `TJ` | Show text with individual glyph positioning |
| `'` | Move to next line and show text |
| `"` | Set spacing, move, and show text |

### Font Types

| Type | Description |
|------|-------------|
| **Type 0** | Composite/CID fonts (for CJK and large character sets) |
| **Type 1** | PostScript outline fonts |
| **TrueType** | TrueType outline fonts |
| **Type 3** | User-defined fonts via PDF content streams |
| **CIDFont** | Component of Type 0 fonts (CIDFontType0 or CIDFontType2) |

### Font Dictionary Key Entries

`/BaseFont`, `/Subtype`, `/Encoding`, `/FirstChar`, `/LastChar`, `/Widths`, `/FontDescriptor`, `/ToUnicode`

### Font Descriptor Key Entries

`/FontName`, `/Flags` (fixed pitch, serif, symbolic, script, italic, all-cap, etc.), `/FontBBox`, `/ItalicAngle`, `/Ascent`, `/Descent`, `/CapHeight`, `/StemV`, `/FontFile` (Type 1), `/FontFile2` (TrueType), `/FontFile3` (CFF or OpenType)

### Encodings

Standard encodings: StandardEncoding, MacRomanEncoding, WinAnsiEncoding, MacExpertEncoding, PDFDocEncoding. Custom differences arrays allow modifications. CID fonts use CMaps for code-to-CID mapping. The `/ToUnicode` CMap enables text extraction.

---

## 12. RENDERING (§10)

Covers halftones (Type 1 spot function, Type 5 named halftones, Type 6/10/16 threshold arrays), transfer functions, rendering intents (AbsoluteColorimetric, RelativeColorimetric, Saturation, Perceptual), overprint control, and black generation/undercolor removal for CMYK workflows.

---

## 13. TRANSPARENCY (§11)

The PDF transparency model (PDF 1.4+):

### Blend Modes

| Category | Modes |
|----------|-------|
| **Basic** | Normal, Multiply, Screen, Overlay |
| **Darkening** | Darken, ColorBurn, HardLight |
| **Lightening** | Lighten, ColorDodge, SoftLight |
| **Arithmetic** | Difference, Exclusion |
| **HSL** | Hue, Saturation, Color, Luminosity |

### Key Concepts

- **Alpha compositing** with constant alpha (`CA`/`ca`) and shape alpha
- **Soft masks** (luminosity-based or alpha-based)
- **Transparency groups** (isolated and/or knockout)
- **Group color spaces** for blending calculations
- Objects composited back-to-front

---

## 14. INTERACTIVE FEATURES (§12)

### Viewer Preferences

Control display: hide toolbar/menubar/window UI, fit window, center window, display document title, page direction (L2R/R2L).

### Navigation

- **Page labels** — Custom numbering schemes (Roman numerals, letters, etc.)
- **Outlines** — Bookmark tree with nested entries
- **Thumbnail images** — Page preview images
- **Article threads** — Reading order across pages

### Annotation Types

| Type | Description |
|------|-------------|
| Text | Sticky notes |
| Link | Hyperlinks |
| FreeText | Directly displayed text |
| Line, Square, Circle | Geometric shapes |
| Polygon, Polyline | Multi-vertex shapes |
| Highlight, Underline, Squiggly, StrikeOut | Text markup |
| Stamp | Rubber stamp appearances |
| Caret | Insertion point marker |
| Ink | Freehand drawings |
| Popup | Pop-up windows for notes |
| FileAttachment | Embedded file references |
| Sound, Movie | Media annotations |
| Widget | Form field appearances |
| Watermark | Background watermarks |
| 3D | 3D artwork annotations |
| Redact | Content redaction |

### Annotation Flags

Invisible, Hidden, Print, NoZoom, NoRotate, NoView, ReadOnly, Locked, ToggleNoView, LockedContents

### Actions

| Type | Description |
|------|-------------|
| GoTo | Navigate within document |
| GoToR | Navigate to remote PDF |
| GoToE | Navigate to embedded PDF |
| Launch | Launch external application |
| URI | Open web URL |
| JavaScript | Execute JavaScript |
| Named | Standard actions (NextPage, PrevPage, FirstPage, LastPage) |
| SubmitForm | Submit form data |
| ResetForm | Reset form fields |
| ImportData | Import FDF data |
| Hide | Show/hide annotations |
| SetOCGState | Toggle optional content layers |
| Rendition | Control multimedia playback |

### Interactive Forms (AcroForm)

| Field Type | Value | Description |
|------------|-------|-------------|
| Text | `/Tx` | Text input |
| Button | `/Btn` | Pushbutton, checkbox, radio |
| Choice | `/Ch` | List box, combo box |
| Signature | `/Sig` | Digital signature |

Key entries: `/V` (value), `/DV` (default value), `/AP` (appearance), `/AA` (additional actions for events).

### Digital Signatures

Signature dictionaries with `/Filter` (handler), `/SubFilter` (format like `adbe.pkcs7.detached`), `/ByteRange` (signed byte ranges), `/Contents` (signature value), `/Cert`, `/Reference` (MDP/UR transforms).

---

## 15. MULTIMEDIA FEATURES (§13)

- **Sound** — Sound objects with sampling rate, channels, bits per sample, encoding
- **Movies** — Movie annotations referencing external files
- **Renditions** (PDF 1.5+) — Rich media content, media clips, players, parameters
- **3D Artwork** (PDF 1.6+) — U3D or PRC streams, 3D views, coordinate systems, animations, JavaScript

---

## 16. DOCUMENT INTERCHANGE (§14)

### Metadata Streams

XMP metadata (XML-based, human-readable) attached to the document or individual objects via `/Metadata` streams.

### Tagged PDF / Logical Structure

Structure tree rooted at `/StructTreeRoot`. Structure elements with `/S` (structure type), `/P` (parent), `/K` (kids).

### Standard Structure Types

| Category | Types |
|----------|-------|
| **Grouping** | Document, Part, Art, Sect, Div, BlockQuote, Caption, TOC, TOCI, Index, NonStruct, Private |
| **Headings** | H1, H2, H3, H4, H5, H6 |
| **Paragraph** | P |
| **Lists** | L, LI, Lbl, LBody |
| **Tables** | Table, TR, TH, TD, THead, TBody, TFoot |
| **Inline** | Span, Quote, Note, Reference, BibEntry, Code |
| **Links** | Link, Annot |
| **Ruby/Warichu** | Ruby, RB, RT, RP, Warichu, WT, WP |
| **Illustration** | Figure, Formula, Form |

### Marked Content Operators

| Operator | Description |
|----------|-------------|
| `BMC` | Begin marked content (tag only) |
| `BDC` | Begin marked content with properties |
| `EMC` | End marked content |

### Accessibility

Tagged PDF enables text extraction, reflow, and assistive technology access. The `/Lang` entry specifies natural language. Alt text, actual text, and expansion text support screen readers.

### Other Features

- **Web Capture** — Content captured from web sources
- **Output Intents** — Color management for production (PDF/X)
- **Presentations** — Page transition effects

---

## 17. COMMON DATA STRUCTURES (§7.9)

| Structure | Format |
|-----------|--------|
| **Date strings** | `D:YYYYMMDDHHmmSSOHH'mm'` with timezone offset |
| **Rectangles** | `[llx lly urx ury]` (lower-left, upper-right corners) |
| **Name trees** | B-tree for string key lookup |
| **Number trees** | B-tree for integer key lookup |
| **Text strings** | PDFDocEncoding or UTF-16BE (BOM: `\xFE\xFF`) |

---

## 18. FUNCTIONS (§7.10)

| Type | Description |
|------|-------------|
| **Type 0** | Sampled — lookup table with interpolation |
| **Type 2** | Exponential — f(x) = C₀ + xⁿ(C₁-C₀) |
| **Type 3** | Stitching — piecewise from subfunctions |
| **Type 4** | PostScript calculator — stack-based mini-language |

All functions take numeric inputs, produce numeric outputs, and have no side effects. Each function defines a domain (input range) and optionally a range (output bounds).

---

## 19. KEY ANNEXES

| Annex | Content |
|-------|---------|
| **A** | Complete operator summary (~70 operators) |
| **B** | Type 4 function operators (PostScript calculator) |
| **C** | Implementation limits (integer range ±2³¹, string max 65535 bytes, nesting depth 28, etc.) |
| **D** | Character sets and encodings (glyph name → Unicode mappings) |
| **E** | PDF Name Registry (developer prefix conventions) |
| **F** | Linearized PDF specification (byte-serving, hint tables) |
| **G** | Linearized PDF access strategies |
| **H** | Example PDF files |
| **I** | PDF version history (1.0 through 1.7 feature additions) |
| **J** | FDF rename flag implementation example |
| **K** | PostScript compatibility for transparent imaging |
| **L** | Colour plates (informative) |

---

## 20. PDF VERSION HISTORY

| Version | Year | Key Features Added |
|---------|------|--------------------|
| 1.0 | 1993 | Initial release |
| 1.1 | 1994 | Encryption, device-independent color, article threads |
| 1.2 | 1996 | Interactive forms, Unicode, CID fonts, halftones |
| 1.3 | 2000 | Digital signatures, JavaScript, logical structure, ICC profiles |
| 1.4 | 2001 | **Transparency**, tagged PDF, encryption improvements |
| 1.5 | 2003 | Cross-reference streams, object streams, JPEG2000, optional content |
| 1.6 | 2005 | OpenType fonts, 3D artwork, AES encryption, NChannel |
| 1.7 | 2006 | Enhanced 3D, XFA forms integration, portable collections |

---

## 21. COMPLETE OPERATOR QUICK REFERENCE

### Graphics State Operators
`q` save, `Q` restore, `cm` concat CTM, `w` line width, `J` line cap, `j` line join, `M` miter limit, `d` dash pattern, `ri` rendering intent, `i` flatness, `gs` set graphics state from ExtGState dict

### Path Operators
`m` moveto, `l` lineto, `c` curveto, `v` curveto2, `y` curveto3, `h` close, `re` rectangle

### Path Painting
`S` stroke, `s` close+stroke, `f`/`F` fill, `f*` fill-eo, `B` fill+stroke, `B*` fill-eo+stroke, `b` close+fill+stroke, `b*` close+fill-eo+stroke, `n` no-op

### Clipping
`W` clip non-zero, `W*` clip even-odd

### Color
`CS`/`cs` set color space, `SC`/`sc`/`SCN`/`scn` set color, `G`/`g` gray, `RG`/`rg` RGB, `K`/`k` CMYK

### Text
`BT`/`ET` begin/end text, `Tc` char space, `Tw` word space, `Tz` h-scale, `TL` leading, `Tf` font, `Tr` render mode, `Ts` rise, `Td`/`TD`/`Tm`/`T*` positioning, `Tj`/`TJ`/`'`/`"` showing

### XObject & Inline Image
`Do` paint XObject, `BI`/`ID`/`EI` inline image

### Shading
`sh` paint shading

### Marked Content
`BMC`/`BDC`/`EMC` marked content, `MP`/`DP` marked-content point

### Compatibility
`BX`/`EX` begin/end compatibility section

---

*Generated from PDF 32000-1:2008 — Document management — Portable document format — Part 1: PDF 1.7*

Source: https://opensource.adobe.com/dc-acrobat-sdk-docs/pdfstandards/PDF32000_2008.pdf
