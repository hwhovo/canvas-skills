---
name: svg-editor
description: SVG (Scalable Vector Graphics) creation, editing, and manipulation based on the W3C SVG 2 specification. Use when generating SVG markup, editing path data, working with viewBox/coordinate systems, gradients, patterns, clip paths, masks, filters, text layout, or any SVG DOM manipulation.
user-invocable: false
---

# Skill: svg-editor

**Domain:** SVG (Scalable Vector Graphics) file creation, editing, and manipulation based on the W3C SVG 2 specification.

---

## SVG DOCUMENT STRUCTURE

**Root element:** `<svg>` with namespace `http://www.w3.org/2000/svg`. Key attributes: `width`, `height`, `viewBox`, `preserveAspectRatio`, `xmlns`. In HTML documents, `xmlns` is not needed.

**Container elements:** `<g>` (grouping), `<defs>` (reusable definitions, never rendered), `<symbol>` (reusable template, only rendered via `<use>`), `<svg>` (nested viewports), `<a>` (hyperlinks), `<switch>` (conditional processing).

**The `<use>` element:** References other elements via `href="#id"`, creates shadow DOM clone. Style inheritance flows through the shadow tree. The `<use>` element forms a non-isolated group for blending.

**Metadata/Descriptive elements:** `<title>` (short text description, first child preferred for accessibility), `<desc>` (longer description), `<metadata>`.

**Never-rendered elements** (always `display:none`): `clipPath`, `defs`, `desc`, `linearGradient`, `marker`, `mask`, `metadata`, `pattern`, `radialGradient`, `script`, `style`, `title`, `symbol` (unless instance root of a use-element shadow tree).

---

## COORDINATE SYSTEM & VIEWBOX

All SVG content is drawn inside **SVG viewports** -- drawing regions with width, height, and origin in abstract **user units**.

**viewBox attribute:** `viewBox="min-x min-y width height"` -- defines the internal coordinate system. Combined with `preserveAspectRatio`, controls scaling/fitting behavior.

**preserveAspectRatio:** Format: `<align> [meetOrSlice]`. Align values: `xMinYMin`, `xMidYMin`, `xMaxYMin`, `xMinYMid`, `xMidYMid` (default), `xMaxYMid`, `xMinYMax`, `xMidYMax`, `xMaxYMax`, or `none`. Meet = fit inside (letterbox), Slice = fill and clip.

**Algorithm:** The spec defines a 14-step algorithm to compute the equivalent transform from viewBox: calculate scale-x = e-width/vb-width, scale-y = e-height/vb-height; if `meet`, use the smaller scale; if `slice`, use the larger; then compute translate offsets based on alignment.

**transform property:** Applies CSS transforms. For SVG elements (except `pattern`, `linearGradient`, `radialGradient`), use the `transform` attribute. For `pattern`, use `patternTransform`; for gradients, use `gradientTransform`. Default `transform-origin` in SVG is `0 0` (not `50% 50%` like CSS).

**Units:** Supported: `em`, `ex`, `px`, `pt`, `pc`, `cm`, `mm`, `in`, percentages. Percentages resolve against the nearest SVG viewport dimensions.

---

## BASIC SHAPES

All basic shapes are mathematically equivalent to `<path>` elements.

**`<rect>`:** Attributes/properties: `x`, `y`, `width`, `height`, `rx`, `ry`. Rounded corners via `rx`/`ry` (auto means copy the other; both auto = 0 = no rounding). Radii clamped to half of width/height. Negative width/height is illegal. Zero width or height disables rendering.

**`<circle>`:** Properties: `cx`, `cy`, `r`. Negative `r` is illegal. Mapped to 4 elliptical arc segments starting at 3 o'clock, going clockwise.

**`<ellipse>`:** Properties: `cx`, `cy`, `rx`, `ry`. Same mapping as circle but with independent radii. `auto` on `rx` or `ry` copies the other value (new in SVG 2).

**`<line>`:** Attributes: `x1`, `y1`, `x2`, `y2`. Lines have no interior, so `fill` has no effect -- only `stroke` renders.

**`<polyline>`:** Attribute: `points` (space/comma-separated coordinate pairs). Defines open connected line segments.

**`<polygon>`:** Attribute: `points`. Same as polyline but automatically closed with a closepath command joining last and first points.

---

## PATH DATA (`<path>` element, `d` property)

The `d` property specifies path data using a prefix notation (commands followed by parameters). Uppercase = absolute coordinates, lowercase = relative.

**Commands:**
- **M/m (moveto):** `M x,y` -- Start new subpath. Subsequent pairs become implicit lineto.
- **Z/z (closepath):** Closes subpath back to initial point with proper stroke-linejoin (vs manual close which uses stroke-linecap).
- **L/l (lineto):** `L x,y` -- Straight line. **H/h** (horizontal), **V/v** (vertical) are shortcuts.
- **C/c (cubic Bezier):** `C x1,y1 x2,y2 x,y` -- Two control points + endpoint. **S/s** (smooth) reflects previous C/S control point automatically.
- **Q/q (quadratic Bezier):** `Q x1,y1 x,y` -- One control point + endpoint. **T/t** (smooth) reflects previous Q/T control point.
- **A/a (elliptical arc):** `A rx,ry x-axis-rotation large-arc-flag sweep-flag x,y` -- Large-arc-flag (0/1), sweep-flag (0/1) select among 4 possible arcs.

**Optimizations:** Whitespace/commas optional where unambiguous. Repeated command letters can be omitted. Concise notation minimizes file size.

**Error handling:** Render up to (but not including) the command containing the first error. Invalid/negative arc radii are auto-corrected (absolute value, scaled up if too small).

---

## PAINTING: FILL & STROKE

**Paint values:** `<paint> = none | <color> | <url> [none | <color>]? | context-fill | context-stroke`

**Fill properties:**
- `fill`: Paint for interior. Default: `black`. Supports colors, gradients (`url(#id)`), patterns, `none`.
- `fill-rule`: `nonzero` (default) or `evenodd` -- determines interior of complex/self-intersecting paths.
- `fill-opacity`: 0-1, default 1.

**Stroke properties:**
- `stroke`: Paint for outline. Default: `none`.
- `stroke-width`: Default `1`. Percentage resolves to viewport size.
- `stroke-linecap`: `butt` (default), `round`, `square` -- shape at open ends.
- `stroke-linejoin`: `miter` (default), `round`, `bevel` -- shape at corners.
- `stroke-miterlimit`: Default `4`. If miter length / stroke-width exceeds this, falls back to bevel.
- `stroke-dasharray`: `none` or comma/space-separated lengths for dash pattern.
- `stroke-dashoffset`: Offset into dash pattern.
- `stroke-opacity`: 0-1.

**paint-order:** `normal | [ fill || stroke || markers ]`. Default rendering order: fill first, then stroke, then markers. Can be reordered.

**Markers:** Drawn at vertices of path/shape elements. Properties: `marker-start`, `marker-mid`, `marker-end`, shorthand `marker`. Reference `<marker>` elements defined in `<defs>`. Marker attributes: `refX`, `refY`, `markerWidth`, `markerHeight`, `orient` (auto | auto-start-reverse | angle), `markerUnits` (strokeWidth | userSpaceOnUse).

---

## PAINT SERVERS: GRADIENTS & PATTERNS

**Linear Gradient (`<linearGradient>`):** Attributes: `x1`, `y1`, `x2`, `y2` (gradient vector), `gradientUnits` (objectBoundingBox default | userSpaceOnUse), `gradientTransform`, `spreadMethod` (pad | reflect | repeat), `href` (inherit from another gradient). Contains `<stop>` children.

**Radial Gradient (`<radialGradient>`):** Attributes: `cx`, `cy`, `r` (outer circle), `fx`, `fy`, `fr` (focal point/inner circle), same gradient attributes as linear.

**Gradient Stops (`<stop>`):** Attributes: `offset` (0-1 or 0%-100%), properties: `stop-color`, `stop-opacity`.

**Pattern (`<pattern>`):** Attributes: `x`, `y`, `width`, `height`, `patternUnits`, `patternContentUnits`, `patternTransform`, `viewBox`, `preserveAspectRatio`, `href`. Contains child graphics elements that define the tile.

**Usage:** `fill="url(#myGradient)"` or `stroke="url(#myPattern) fallback-color"`.

---

## TEXT

**`<text>` element:** Primary text container. Attributes: `x`, `y` (list of coordinates for each glyph position), `dx`, `dy` (relative offsets), `rotate` (list of rotation angles), `textLength`, `lengthAdjust`.

**`<tspan>`:** Inline child for styling spans within text. Same positioning attributes as `<text>`.

**`<textPath>`:** Places text along a path. Attribute: `href` references a path element, `startOffset`, `method` (align | stretch), `spacing` (auto | exact), `side` (left | right).

**Text rendering modes:** Pre-formatted (default, limited wrapping), auto-wrapped (if content area via `inline-size` is provided -- new in SVG 2), text on a path.

**Key text properties:** `font-family`, `font-size`, `font-weight`, `font-style`, `font-variant`, `text-anchor` (start | middle | end), `dominant-baseline`, `alignment-baseline`, `text-decoration`, `letter-spacing`, `word-spacing`, `writing-mode`, `direction`, `white-space`.

---

## GEOMETRY PROPERTIES (SVG 2 CSS Properties)

These are now CSS properties (not just attributes):
- `cx`, `cy`: Center coordinates (circle, ellipse)
- `r`: Radius (circle only). Negative = illegal.
- `rx`, `ry`: Horizontal/vertical radii (ellipse, rect). `auto` copies the other value.
- `x`, `y`: Position (svg, rect, image, foreignObject)
- `width`, `height`: Sizing (rect, svg, image, foreignObject). `auto` on `<svg>` = 100%. `auto` on `<image>` = intrinsic size.

---

## RENDERING MODEL

**Painters model:** Later elements paint over earlier ones. No z-index reordering (except via stacking contexts).

**Stacking contexts created by:** root element, outermost `<svg>`, `foreignObject`, `image`, `marker`, `mask`, `pattern`, `symbol`, `use`, or when `opacity` < 1, `clip-path` != none, `mask` != none, `filter` != none, `overflow` != visible on inner `<svg>`.

**display vs visibility:** `display:none` removes from rendering tree entirely (no events, no bounding box). `visibility:hidden` keeps in rendering tree (still gets events per pointer-events, contributes to bounding box, affects text layout).

**opacity:** Group opacity renders group to offscreen buffer first, then composites with specified opacity. `fill-opacity` and `stroke-opacity` are intermediate operations within a single element.

---

## STYLING

**Three mechanisms:** CSS stylesheets (`<style>` element or external), inline `style` attribute, presentation attributes.

**Cascade priority (lowest to highest):** Presentation attributes (specificity 0, author level, after all other author stylesheets) -> CSS rules -> inline style.

**Presentation attributes:** Most SVG properties have corresponding presentation attributes (same name as property in lowercase). Geometry properties (`cx`, `cy`, `r`, `rx`, `ry`, `x`, `y`, `d`, `width`, `height`) only work as presentation attributes on specific designated elements. `transform` maps to `patternTransform` on `<pattern>` and `gradientTransform` on gradients.

**User agent stylesheet highlights:** `svg:not(:root), image, marker, pattern, symbol { overflow: hidden; }`, `*:not(svg) { transform-origin: 0 0; }`, defs/clipPath/mask/marker/desc/title/metadata/pattern/gradients/script/style/symbol get `display: none !important`.

---

## EMBEDDED CONTENT

**`<image>`:** References raster (PNG, JPEG required) or SVG files via `href`. Properties: `x`, `y`, `width`, `height`. `preserveAspectRatio` controls fitting. Supports `object-fit`/`object-position` (CSS). `auto` width/height sizes to intrinsic dimensions (new in SVG 2). Overflow hidden by default.

**`<foreignObject>`:** Embeds non-SVG content (HTML, MathML). Properties: `x`, `y`, `width`, `height`. Creates a CSS containing block for child content. Subject to SVG transforms, filters, clipping, masking.

**HTML elements in SVG:** `<video>`, `<audio>`, `<iframe>`, `<canvas>` from HTML namespace can be used directly as children of SVG container elements.

---

## LINKING

**`<a>` element:** Wraps any content to create hyperlinks. Attributes: `href`, `target` (_self | _parent | _top | _blank | name), `download`, `rel`, `hreflang`, `type`, `referrerpolicy`. Links cannot be nested.

**URL references:** `href` attribute (preferred, no namespace) replaces deprecated `xlink:href`. Paint references use `url(#id)` syntax in CSS/presentation attributes.

**Fragment identifiers:** Bare name (`#MyView`), SVG view spec (`#svgView(viewBox(0,0,200,200))`), or media fragments (`#xywh=0,200,1000,1000`, `#t=10`).

---

## SCRIPTING & INTERACTIVITY

**`<script>` element:** Default type `application/ecmascript`. Supports `href` for external scripts, `crossorigin`. Aligned with HTML `<script>`.

**Events:** Full UI Events support (click, mouse*, key*, focus*, etc.). Animation events: `beginEvent`, `endEvent`, `repeatEvent`. Event attributes on all SVG elements (`onclick`, `onload`, `onerror`, etc.).

**pointer-events property:** Controls hit-testing. Values: `visiblePainted` (default), `visibleFill`, `visibleStroke`, `visible`, `painted`, `fill`, `stroke`, `all`, `none`, `bounding-box`. Masks don't prevent pointer events; clip-paths do.

**Focus/Keyboard:** SVG uses HTML focus model. Focusable elements: root, `<svg>` with zoom controls, valid links, elements with `tabindex`. Sequential focus order follows `tabindex` rules.

---

## ANIMATION

**Four methods:** SVG animation elements (SMIL-based: `<animate>`, `<animateTransform>`, `<animateMotion>`, `<set>`, `<discard>`), CSS Animations, CSS Transitions, Web Animations API / SVG DOM scripting.

---

## CLIPPING & MASKING

**`<clipPath>`:** Binary geometric clipping. Contains shapes/text defining clip region. `clipPathUnits`: objectBoundingBox | userSpaceOnUse. Applied via `clip-path` property.

**`<mask>`:** Alpha/luminance masking for semi-transparency. `maskUnits`, `maskContentUnits`. Applied via `mask` property. Pointer events still captured even where mask is transparent.

---

## FILTER EFFECTS

Applied via `filter` property referencing `<filter>` element. Filter primitives include: `feBlend`, `feColorMatrix`, `feComponentTransfer`, `feComposite`, `feConvolveMatrix`, `feDiffuseLighting`, `feDisplacementMap`, `feDistantLight`, `feDropShadow`, `feFlood`, `feGaussianBlur`, `feImage`, `feMerge`, `feMorphology`, `feOffset`, `fePointLight`, `feSpecularLighting`, `feSpotLight`, `feTile`, `feTurbulence`. Filters have no effect on pointer event processing.

---

## ACCESSIBILITY

- Use `<title>` (short label) and `<desc>` (longer description) as first children of elements
- Full WAI-ARIA support: `role`, `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-hidden`, etc. on all SVG elements
- `tabindex` for keyboard navigation
- `lang` attribute for language identification
- Grouping with `<g>` + ARIA roles for semantic structure

---

## COMPLETE ELEMENT LIST

`a`, `animate`, `animateMotion`, `animateTransform`, `audio`, `canvas`, `circle`, `clipPath`, `defs`, `desc`, `discard`, `ellipse`, `feBlend`, `feColorMatrix`, `feComponentTransfer`, `feComposite`, `feConvolveMatrix`, `feDiffuseLighting`, `feDisplacementMap`, `feDistantLight`, `feDropShadow`, `feFlood`, `feFuncA/B/G/R`, `feGaussianBlur`, `feImage`, `feMerge`, `feMergeNode`, `feMorphology`, `feOffset`, `fePointLight`, `feSpecularLighting`, `feSpotLight`, `feTile`, `feTurbulence`, `filter`, `foreignObject`, `g`, `iframe`, `image`, `line`, `linearGradient`, `marker`, `mask`, `metadata`, `mpath`, `path`, `pattern`, `polygon`, `polyline`, `radialGradient`, `rect`, `script`, `set`, `stop`, `style`, `svg`, `switch`, `symbol`, `text`, `textPath`, `title`, `tspan`, `unknown`, `use`, `video`, `view`

---

## KEY SVG-SPECIFIC PROPERTIES (with defaults)

| Property | Default |
|---|---|
| `fill` | `black` |
| `fill-opacity` | `1` |
| `fill-rule` | `nonzero` |
| `stroke` | `none` |
| `stroke-width` | `1` |
| `stroke-linecap` | `butt` |
| `stroke-linejoin` | `miter` |
| `stroke-miterlimit` | `4` |
| `stroke-dasharray` | `none` |
| `stroke-dashoffset` | `0` |
| `stroke-opacity` | `1` |
| `opacity` | `1` |
| `paint-order` | `normal` |
| `color-interpolation` | `sRGB` |
| `pointer-events` | `visiblePainted` |
| `shape-rendering` | `auto` |
| `text-rendering` | `auto` |
| `image-rendering` | `auto` |
| `vector-effect` | `none` |
| `text-anchor` | `start` |
| `dominant-baseline` | `auto` |
| `marker-start/mid/end` | `none` |
| `stop-color` | `black` |
| `stop-opacity` | `1` |
| `flood-color` | `black` |
| `flood-opacity` | `1` |
| `visibility` | `visible` |
| `overflow` | see UA stylesheet |
| `display` | `inline` |
