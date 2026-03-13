---
name: fabricjs-canvas
description: Use when working with Fabric.js, HTML Canvas 2D, SVG parsing/decomposition, rendering engine code, element adapters, overlays, export pipeline, or any canvas drawing logic. Triggers on fabric imports, CanvasRenderingContext2D, SVG manipulation, DOMParser SVG, getBBox, viewBox, AFTER_RENDER, overlay compositing, export clip paths, element adapter creation, or canvas rendering modifications.
user-invocable: false
---

# Fabric.js Canvas & HTML5 Canvas 2D — Comprehensive Skill Guide

## 1. HTML5 Canvas API Fundamentals

### 1.1 Basic Usage
The `<canvas>` element creates a fixed-size drawing surface with one or more rendering contexts. It has only two attributes: `width` and `height` (default 300x150 pixels). The element can be styled with CSS but the rendering uses the attribute dimensions.

```html
<canvas id="myCanvas" width="600" height="400"></canvas>
```

```javascript
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');
// Fallback content goes between the tags for unsupported browsers
```

### 1.2 Drawing Shapes

**Rectangles** — the only primitive shape directly supported:
- `fillRect(x, y, width, height)` — filled rectangle
- `strokeRect(x, y, width, height)` — outline rectangle
- `clearRect(x, y, width, height)` — clears area to transparent

**Paths** — the fundamental building block for all other shapes:
```javascript
ctx.beginPath();           // Start new path
ctx.moveTo(x, y);         // Move pen
ctx.lineTo(x, y);         // Draw line
ctx.arc(x, y, r, startAngle, endAngle, counterclockwise);
ctx.arcTo(x1, y1, x2, y2, radius);
ctx.quadraticCurveTo(cp1x, cp1y, x, y);
ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y);
ctx.rect(x, y, w, h);     // Rectangle path
ctx.ellipse(x, y, rx, ry, rotation, startAngle, endAngle);
ctx.closePath();           // Close path back to start
ctx.fill();                // Fill the path
ctx.stroke();              // Stroke the path
```

**Path2D** — reusable path objects, supports SVG path data:
```javascript
const path = new Path2D('M10 10 h 80 v 80 h -80 Z');
ctx.fill(path);
```

### 1.3 Styles and Colors

**Colors and Transparency:**
```javascript
ctx.fillStyle = 'rgba(255, 0, 0, 0.5)';   // Fill color
ctx.strokeStyle = '#0000FF';                 // Stroke color
ctx.globalAlpha = 0.5;                       // Global transparency
```

**Line Styles:**
```javascript
ctx.lineWidth = 2;
ctx.lineCap = 'butt' | 'round' | 'square';
ctx.lineJoin = 'round' | 'bevel' | 'miter';
ctx.miterLimit = 10;
ctx.setLineDash([5, 15]);    // Dash pattern
ctx.lineDashOffset = 0;      // Dash offset
```

**Gradients:**
```javascript
// Linear gradient
const lg = ctx.createLinearGradient(x0, y0, x1, y1);
lg.addColorStop(0, 'white');
lg.addColorStop(1, 'black');
ctx.fillStyle = lg;

// Radial gradient
const rg = ctx.createRadialGradient(x0, y0, r0, x1, y1, r1);

// Conic gradient
const cg = ctx.createConicGradient(angle, x, y);
```

**Patterns:**
```javascript
const pattern = ctx.createPattern(image, 'repeat' | 'repeat-x' | 'repeat-y' | 'no-repeat');
ctx.fillStyle = pattern;
```

**Shadows:**
```javascript
ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
ctx.shadowBlur = 5;
ctx.shadowColor = 'rgba(0, 0, 0, 0.5)';
```

### 1.4 Drawing Text
```javascript
ctx.font = '48px serif';
ctx.textAlign = 'start' | 'end' | 'left' | 'right' | 'center';
ctx.textBaseline = 'top' | 'hanging' | 'middle' | 'alphabetic' | 'ideographic' | 'bottom';
ctx.direction = 'ltr' | 'rtl' | 'inherit';

ctx.fillText('Hello', x, y, maxWidth);    // Filled text
ctx.strokeText('Hello', x, y, maxWidth);   // Outlined text
const metrics = ctx.measureText('Hello');   // TextMetrics object
```

### 1.5 Using Images
```javascript
// Sources: HTMLImageElement, SVGImageElement, HTMLVideoElement,
// HTMLCanvasElement, ImageBitmap, OffscreenCanvas, VideoFrame

// Draw image (3 variants):
ctx.drawImage(image, dx, dy);                              // Position only
ctx.drawImage(image, dx, dy, dw, dh);                     // Position + scale
ctx.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);    // Slice + position + scale
```

### 1.6 Transformations
```javascript
// State management
ctx.save();      // Push state to stack
ctx.restore();   // Pop state from stack

// Transform methods
ctx.translate(x, y);
ctx.rotate(angle);           // In radians
ctx.scale(x, y);
ctx.transform(a, b, c, d, e, f);      // Multiply current matrix
ctx.setTransform(a, b, c, d, e, f);   // Reset and set matrix
ctx.resetTransform();                   // Reset to identity

// DOMMatrix support
const matrix = ctx.getTransform();      // Returns DOMMatrix
ctx.setTransform(domMatrix);
```

### 1.7 Compositing and Clipping
```javascript
// Compositing
ctx.globalCompositeOperation = 'source-over';  // default
// Other values: source-in, source-out, source-atop,
// destination-over, destination-in, destination-out, destination-atop,
// lighter, copy, xor, multiply, screen, overlay, darken, lighten,
// color-dodge, color-burn, hard-light, soft-light, difference,
// exclusion, hue, saturation, color, luminosity

// Clipping
ctx.beginPath();
ctx.arc(100, 100, 75, 0, Math.PI * 2);
ctx.clip();  // Everything drawn after this is clipped to the circle
// Use ctx.clip('evenodd') for even-odd fill rule
```

### 1.8 Animations

**Basic Animation Loop:**
```javascript
function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  // Update and draw objects
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

**Advanced techniques:**
- Use `requestAnimationFrame()` for smooth 60fps rendering
- Implement delta-time for frame-rate independent movement
- Use `setInterval()` only when you need constant, slow-speed updates
- Boundary detection and collision handling
- Acceleration, friction, and trail effects via semi-transparent clearRect

### 1.9 Pixel Manipulation
```javascript
// Create empty ImageData
const imageData = ctx.createImageData(width, height);

// Get pixel data from canvas
const imageData = ctx.getImageData(x, y, width, height);

// Put pixel data back
ctx.putImageData(imageData, dx, dy);

// Access pixels (RGBA, 4 bytes per pixel)
const data = imageData.data;  // Uint8ClampedArray
for (let i = 0; i < data.length; i += 4) {
  const r = data[i];     // Red (0-255)
  const g = data[i + 1]; // Green
  const b = data[i + 2]; // Blue
  const a = data[i + 3]; // Alpha
}

// Note: Tainted canvas (cross-origin images) blocks getImageData()
```

### 1.10 Canvas Optimization
- **Avoid unnecessary state changes** — batch similar draws together
- **Use layered canvases** — separate static background from dynamic foreground
- **Pre-render to off-screen canvas** for complex static content
- **Use integer coordinates** to avoid sub-pixel anti-aliasing
- **Minimize canvas API calls** — batch operations, reduce state changes
- **Use CSS transforms** for scaling/rotating the canvas element itself
- **Avoid `shadowBlur`** in animations (very expensive)
- **Clear only dirty regions** instead of full canvas
- **Use `requestAnimationFrame`** not `setInterval`
- **Avoid floating-point coordinates** for crisp rendering

---

## 2. Fabric.js Framework

### 2.1 Installation
```bash
npm install fabric        # Latest (v7+)
npm install fabric@5      # Version 5.x
npm install fabric@6      # Version 6.x
```

```html
<!-- CDN -->
<script src="https://cdn.jsdelivr.net/npm/fabric@latest/dist/index.min.js"></script>
```

### 2.2 Hello World
```javascript
import { Canvas, Rect } from 'fabric';

const canvas = new Canvas('myCanvas');
const rect = new Rect({
  left: 100,
  top: 100,
  width: 50,
  height: 50,
  fill: 'red',
  angle: 30
});
canvas.add(rect);
```

### 2.3 Core Concepts

**Object Model** — Everything on canvas is a `FabricObject`:
- `Rect`, `Circle`, `Ellipse`, `Triangle`, `Polygon`, `Polyline`, `Line`
- `FabricText`, `Textbox`, `IText`
- `FabricImage`
- `Group`, `ActiveSelection`
- `Path` (SVG paths)

**Canvas Types:**
- `Canvas` — Full interactive canvas with mouse/touch events, selection, dragging
- `StaticCanvas` — Rendering only, no interaction (lighter weight)

**Key Properties (all objects):**
```javascript
{
  left: 0, top: 0,           // Position
  width: 100, height: 100,   // Dimensions
  scaleX: 1, scaleY: 1,      // Scale
  angle: 0,                   // Rotation (degrees)
  fill: 'red',               // Fill color/gradient/pattern
  stroke: 'black',           // Stroke color
  strokeWidth: 1,            // Stroke width
  opacity: 1,                // 0-1
  visible: true,
  selectable: true,
  evented: true,
  originX: 'center',         // v7 default (was 'left' in v6)
  originY: 'center',         // v7 default (was 'top' in v6)
  clipPath: null,            // ClipPath object
  shadow: null,              // Shadow object
}
```

### 2.4 Events System
```javascript
// Object events
object.on('selected', (e) => {});
object.on('deselected', (e) => {});
object.on('modified', (e) => {});
object.on('rotating', (e) => {});
object.on('scaling', (e) => {});
object.on('moving', (e) => {});
object.on('mousedown', (e) => {});
object.on('mouseup', (e) => {});
object.on('mouseover', (e) => {});

// Canvas events
canvas.on('object:added', (e) => {});
canvas.on('object:removed', (e) => {});
canvas.on('object:modified', (e) => {});
canvas.on('selection:created', (e) => {});
canvas.on('selection:updated', (e) => {});
canvas.on('selection:cleared', (e) => {});
canvas.on('mouse:down', (e) => {});
canvas.on('mouse:move', (e) => {});
canvas.on('mouse:up', (e) => {});
canvas.on('mouse:wheel', (e) => {});
canvas.on('before:render', (e) => {});
canvas.on('after:render', (e) => {});

// Remove listeners
object.off('selected');
object.off();  // Remove all
```

### 2.5 Serialization (JSON & SVG)
```javascript
// Export to JSON
const json = canvas.toJSON();
const jsonWithCustom = canvas.toJSON(['customProp1', 'customProp2']);

// Load from JSON
canvas.loadFromJSON(json);
await canvas.loadFromJSON(json);  // v6+ returns promise

// Export to SVG
const svg = canvas.toSVG();

// Export to data URL
const dataURL = canvas.toDataURL({
  format: 'png',   // or 'jpeg'
  quality: 0.8,
  multiplier: 2    // for retina
});
```

### 2.6 Object Caching
Fabric uses per-object caching for performance. Each object renders to its own off-screen canvas, and the cache is reused until the object changes.

```javascript
// Disable caching per object
object.objectCaching = false;

// Disable globally
fabric.Object.prototype.objectCaching = false;

// Key cache properties
object.statefullCache = false;   // Whether to check all props for cache invalidation
object.cacheProperties = [...];  // Properties that trigger cache invalidation
object.dirty = true;             // Force cache refresh

// Cache sizing
object.needsItsOwnCache();      // Check if object needs separate cache
```

**When caching helps:** Complex objects, objects with shadows/filters, text
**When to disable:** Heavily animated objects, pixel-perfect rendering needs, memory concerns

### 2.7 Transformations
```javascript
// Fabric uses a transformation matrix: [a, b, c, d, e, f]
// Similar to CSS matrix(a, b, c, d, e, f)

// Viewport transform (pan/zoom)
canvas.viewportTransform = [zoom, 0, 0, zoom, panX, panY];
canvas.setViewportTransform([1, 0, 0, 1, 0, 0]); // Reset
canvas.zoomToPoint(point, zoom);
canvas.relativePan(new fabric.Point(dx, dy));

// Object transform
object.calcTransformMatrix();           // Full transform matrix
object.calcOwnMatrix();                 // Object's own transform (no group)

// Coordinate conversion
const viewportPoint = fabric.util.transformPoint(objectPoint, canvas.viewportTransform);
const objectPoint = fabric.util.transformPoint(viewportPoint, fabric.util.invertTransform(canvas.viewportTransform));

// Utility functions
fabric.util.multiplyTransformMatrices(m1, m2);
fabric.util.invertTransform(matrix);
fabric.util.decomposeMatrix(matrix);  // Returns { angle, scaleX, scaleY, skewX, skewY, translateX, translateY }
fabric.util.composeMatrix(options);    // Reverse of decompose
```

### 2.8 Configuring Controls
```javascript
import { controlsUtils, Control } from 'fabric';

// Customize existing controls
object.controls.mtr = new Control({
  x: 0,
  y: -0.5,
  offsetY: -40,
  cursorStyle: 'crosshair',
  actionHandler: controlsUtils.rotationWithSnapping,
  actionName: 'rotate',
  render: myCustomRenderFunction,    // Custom icon rendering
  cornerSize: 28,
  withConnection: true,
});

// Add custom control
object.controls.myControl = new Control({
  x: 0.5,
  y: -0.5,
  offsetX: 16,
  offsetY: -16,
  cursorStyle: 'pointer',
  mouseUpHandler: (eventData, transform) => {
    const target = transform.target;
    target.set('fill', 'blue');
    target.canvas.requestRenderAll();
  },
  render: (ctx, left, top, styleOverride, fabricObject) => {
    ctx.save();
    ctx.translate(left, top);
    ctx.beginPath();
    ctx.arc(0, 0, 10, 0, Math.PI * 2);
    ctx.fill();
    ctx.restore();
  },
});

// Control visibility
object.setControlVisible('tl', false);
object.setControlsVisibility({
  mt: false, mb: false,
  mtr: true,
});

// Corner styling
object.set({
  cornerStyle: 'circle',
  cornerColor: 'blue',
  cornerStrokeColor: 'white',
  cornerSize: 12,
  transparentCorners: false,
  borderColor: 'red',
  borderDashArray: [5, 5],
  padding: 10,
});
```

### 2.9 Custom Properties
```javascript
const rect = new Rect({
  width: 100, height: 100, fill: 'red',
  myCustomProp: 'hello',
  data: { id: 123, type: 'button' }
});

rect.myCustomProp;  // 'hello'
rect.data.id;       // 123

// Include in serialization
canvas.toJSON(['myCustomProp', 'data']);

// Custom reviver for deserialization
canvas.loadFromJSON(json, () => {}, (o, object) => {
  // Called for each object
});
```

### 2.10 ClipPath System
ClipPath allows any Fabric object to act as a clipping mask.

```javascript
// Basic clipPath
const clipCircle = new Circle({
  radius: 50, originX: 'center', originY: 'center'
});
rect.clipPath = clipCircle;

// Inverted clipPath (show everything EXCEPT the clip area)
clipCircle.inverted = true;

// Absolute positioning (relative to canvas, not object)
clipCircle.absolutePositioned = true;

// Group as clipPath
const clipGroup = new Group([circle1, circle2]);
rect.clipPath = clipGroup;

// Canvas-level clipPath
canvas.clipPath = new Rect({
  width: 400, height: 400, originX: 'center', originY: 'center'
});

// Nested clipPaths
const innerClip = new Circle({ radius: 30 });
const outerClip = new Rect({ width: 100, height: 100 });
outerClip.clipPath = innerClip;
object.clipPath = outerClip;
```

### 2.11 Filters (Image)
```javascript
import { FabricImage, filters } from 'fabric';

const img = await FabricImage.fromURL('image.png');

img.filters.push(new filters.Grayscale());
img.filters.push(new filters.Brightness({ brightness: 0.1 }));
img.filters.push(new filters.Contrast({ contrast: 0.2 }));
img.filters.push(new filters.Blur({ blur: 0.5 }));
img.filters.push(new filters.Noise({ noise: 100 }));
img.filters.push(new filters.Pixelate({ blocksize: 4 }));
img.filters.push(new filters.Invert());
img.filters.push(new filters.Sepia());
img.filters.push(new filters.RemoveColor({ color: '#ffffff', distance: 0.1 }));
img.filters.push(new filters.BlendColor({ color: 'red', mode: 'tint', alpha: 0.5 }));
img.filters.push(new filters.Gamma({ gamma: [1, 0.5, 2.1] }));
img.filters.push(new filters.Saturation({ saturation: 0.7 }));
img.filters.push(new filters.HueRotation({ rotation: 0.5 }));
img.filters.push(new filters.Convolute({ matrix: [0, -1, 0, -1, 5, -1, 0, -1, 0] }));
img.filters.push(new filters.ColorMatrix({ matrix: [...] }));

img.applyFilters();
canvas.renderAll();

img.filters = [];
img.applyFilters();  // Remove all filters
```

### 2.12 Text Handling
```javascript
// Static text
const text = new FabricText('Hello World', {
  fontFamily: 'Arial', fontSize: 24, fontWeight: 'bold', fontStyle: 'italic',
  underline: true, textAlign: 'center', lineHeight: 1.2, charSpacing: 100,
  textBackgroundColor: 'yellow', fill: 'black',
});

// Interactive text (double-click to edit)
const itext = new IText('Edit me', { ... });

// Auto-wrapping textbox
const textbox = new Textbox('Long text wraps automatically', {
  width: 200, splitByGrapheme: false,
});

// Rich text (per-character styling)
itext.setSelectionStyles({ fill: 'red', fontSize: 30 }, 0, 5);

// Superscript/Subscript
text.setSuperscript(0, 3);
text.setSubscript(5, 8);
```

### 2.13 Groups
```javascript
const group = new Group([rect, circle, text], { left: 100, top: 100 });
canvas.add(group);

group.getObjects();
group.item(0);
group.add(newObject);
group.remove(existingObject);

// Interactive group (v7)
group.interactive = true;
group.subTargetCheck = true;

// Ungroup
const items = group.removeAll();
canvas.remove(group);
items.forEach(item => canvas.add(item));
```

### 2.14 Free Drawing
```javascript
canvas.isDrawingMode = true;

canvas.freeDrawingBrush = new fabric.PencilBrush(canvas);
canvas.freeDrawingBrush.color = 'red';
canvas.freeDrawingBrush.width = 5;

canvas.on('path:created', (e) => { const path = e.path; });
```

### 2.15 Zoom and Pan
```javascript
canvas.on('mouse:wheel', (opt) => {
  let zoom = canvas.getZoom() * (0.999 ** opt.e.deltaY);
  zoom = Math.min(Math.max(zoom, 0.01), 20);
  canvas.zoomToPoint(new fabric.Point(opt.e.offsetX, opt.e.offsetY), zoom);
  opt.e.preventDefault();
  opt.e.stopPropagation();
});
```

---

## 3. Fabric.js Source Code Architecture

### 3.1 Repository Structure
```
fabric.js/src/
  canvas/          - Canvas and StaticCanvas implementations
    Canvas.ts      - Interactive canvas (mouse/touch/keyboard events)
    StaticCanvas.ts - Base canvas (rendering only)
    SelectableCanvas.ts - Selection handling mixin
  shapes/          - All shape objects
    Object/        - Base FabricObject (rendering, transforms, caching)
    Rect.ts, Circle.ts, Ellipse.ts, Triangle.ts
    Polygon.ts, Polyline.ts, Line.ts, Path.ts
    Text/, Textbox.ts, IText/
    Image.ts, Group.ts, ActiveSelection.ts
  parser/          - SVG parsing subsystem
    parseAttributes.ts, parseSVGDocument.ts
    parseStyleAttribute.ts, parseTransformAttribute.ts
    elements/      - Individual SVG element parsers
  controls/        - Object controls (resize, rotate handles)
    Control.ts     - Base control class
    controlHandlers.ts, commonControls.ts, polyControl.ts
    rotate.ts, scale.ts, drag.ts
  filters/         - Image filters (WebGL + Canvas2D)
  brushes/         - Free drawing brushes
  gradient/        - Gradient implementation
  pattern/         - Pattern fill
  shadow/          - Shadow implementation
  color/           - Color parsing and manipulation
  util/            - Utility functions (transforms, math, DOM)
  typedefs/        - TypeScript type definitions
  config.ts        - Global configuration
  env.ts           - Environment detection
  cache.ts         - Char width cache for text
```

### 3.2 Key Design Patterns
- **TypeScript-first** since v6 (complete rewrite from ES5 classes)
- **Tree-shakeable** ES modules
- **Per-object caching** with dirty flag system
- **Promise-based async** (v6+ replaced callbacks)
- **Transform matrix pipeline**: object -> group -> viewport
- **Event delegation**: Canvas captures DOM events, translates to fabric events
- **LayoutManager** (v7): handles group layout strategies (fit-content, fixed, clip-path)

### 3.3 Class Hierarchy
```
CommonMethods (events, state)
  -> FabricObject (rendering, transforms, caching, controls)
    -> Rect, Circle, Ellipse, Triangle, Line
    -> Polyline -> Polygon
    -> Path
    -> FabricText -> IText -> Textbox
    -> FabricImage
    -> Group -> ActiveSelection

StaticCanvas (rendering, serialization, object management)
  -> SelectableCanvas (selection logic)
    -> Canvas (full interactivity)
```

---

## 4. Known SVG Parser Issues (21 open)

### Critical/High Impact:
1. **Group with fill attribute ignores children** — fill doesn't cascade to child elements
2. **Filter, mask, and pattern elements not supported** — major SVG features missing
3. **Text positioning incorrect** — `dy` attributes and tspan positioning broken
4. **Infinite recursion on pattern parsing** — patterns referencing patterns → stack overflow
5. **Gradient transforms not applied** — `gradientTransform` attribute ignored

### Medium Impact:
6. viewBox scaling issues
7. stroke-dasharray parsing — complex dash arrays broken
8. CSS class selectors not supported — only inline styles parsed
9. Embedded fonts ignored — @font-face in SVG not processed
10. clip-path in SVG not converted to Fabric clipPath
11. Nested `<svg>` elements not handled
12. Marker elements not supported
13. Complex `<use>`/`<defs>` resolution issues
14. Percentage values not properly resolved
15. `<switch>`/`<foreignObject>` ignored

### Lower Priority:
16-21. Edge cases with opacity inheritance, transform origins, text decoration, unicode-bidi, symbol elements, metadata preservation.

---

## 5. Known ClipPath Issues (3 open)

1. **Group with shadow + clipPath renders incorrectly** — shadow gets clipped or positioned wrong
2. **Scaling + stroke + clipPath interaction** — stroke doesn't scale correctly relative to clip boundary
3. **Free drawing doesn't respect canvas clipPath** — paths appear outside clip during drawing, clipped only after finalization

---

## 6. Best Practices & Optimization

### Performance:
- Enable `objectCaching` (default) for complex objects
- Use `StaticCanvas` when interactivity isn't needed
- Use `canvas.renderOnAddRemove = false` for batch operations, then `canvas.requestRenderAll()`
- Minimize shadows and filters in animations
- Use `skipOffscreen = true` (default) to skip rendering off-screen objects
- Set `dirty = false` on objects that haven't changed

### Memory:
- Dispose canvases properly: `canvas.dispose()`
- Clean up event listeners: `object.off()`
- Use appropriate cache sizes
- Remove unused objects from canvas

### Architecture:
- Use Groups to organize related objects
- Leverage the event system for loose coupling
- Use `toJSON()`/`loadFromJSON()` for save/restore
- Implement undo/redo via JSON snapshots or command pattern
- Use `enlivenObjects()` for custom deserialization

### Upgrading:
- **v5 → v6:** TypeScript rewrite, callbacks → Promises, ES modules, class-based
- **v6 → v7:** `originX/originY` defaults changed to `'center'` (was `'left'/'top'`), LayoutManager for groups, deprecated API cleanup

---

## 7. Common Gotchas

1. **`canvas.clipPath` with `destination-in`** — Fabric's `drawClipPathOnCanvas` uses `destination-in` internally; combining with your own compositing in `after:render` may conflict. Use post-render processing on a separate canvas.
2. **`getBBox()` returns local coordinates** — must multiply by `getCTM()` for absolute positioning, especially with Illustrator SVGs using matrix transforms.
3. **Temp SVG for measurement** — use `position:fixed;left:-99999px`, NOT `width:0;height:0;overflow:hidden` (browsers skip text glyph layout in zero-sized containers).
4. **`after:render` overlays NOT in exports** — `toCanvasElement()` renders on a temp canvas without `after:render`. Inject temp Fabric objects before export.
5. **`overlayImage` viewport issues** — scales with viewport transform, shifts during zoom/pan. Draw on canvas context in `after:render` instead.
6. **Export viewport transform** — reset viewport, resize canvas to export dimensions, then restore after.
7. **Batch operations** — set `renderOnAddRemove = false`, do all operations, then `requestRenderAll()` once.
8. **Origin defaults changed in v7** — v5/v6: `'left'`/`'top'`; v7: `'center'`/`'center'`.

---

## Sources
- [MDN Canvas API Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [Fabric.js Official Documentation](https://fabricjs.com/docs/)
- [Fabric.js GitHub Repository](https://github.com/fabricjs/fabric.js)
- [Fabric.js SVG Parser Issues](https://github.com/fabricjs/fabric.js/issues?q=label%3Asvg_parser)
- [Fabric.js ClipPath Issues](https://github.com/fabricjs/fabric.js/issues?q=label%3AclipPath)
