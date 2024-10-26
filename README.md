# HTML Canvas Complete Cheat Sheet

A comprehensive guide to working with the HTML Canvas API for 2D graphics and animations.

## Table of Contents
- [Basic Setup](#basic-setup)
- [Canvas Context](#canvas-context)
- [Transformations](#transformations)
- [Drawing Images](#drawing-images)
- [Compositing](#compositing)
- [Line Styling](#line-styling)
- [Colors and Styles](#colors-and-styles)
- [Path Operations](#path-operations)
- [Rectangles](#rectangles)
- [Pixel Manipulation](#pixel-manipulation)
- [Text Operations](#text-operations)
- [Best Practices](#best-practices)

## Basic Setup

### Canvas Element

The canvas element provides a drawing surface for 2D graphics.

```html
<canvas id="myCanvas" width="300" height="150"></canvas>
```

**How it works:**
The canvas element creates a rectangular drawing area in your HTML document. When you specify width and height:
- These are the actual pixel dimensions of the drawing surface
- Different from CSS width/height which scale the canvas
- If not specified, defaults to 300×150 pixels
- The canvas creates a blank, fully transparent surface

**Attributes:**
- `width`: Canvas width in pixels (default: 300)
- `height`: Canvas height in pixels (default: 150)

**Methods:**
```javascript
// Convert canvas to image
const dataURL = canvas.toDataURL('image/png');
// Get drawing context
const ctx = canvas.getContext('2d');
```

**How the methods work:**
- `toDataURL()`: Creates a base64-encoded URL containing canvas image data
  - Optional 'type' parameter specifies format (default: 'image/png')
  - Can be used to save canvas content as images
- `getContext()`: Obtains the drawing interface
  - '2d' is most common context type
  - Returns null if context type isn't supported
  - Same context is returned for multiple calls

## Canvas Context

The 2D rendering context is the primary interface for drawing.

```javascript
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');

// State management
ctx.save();     // Push current state to stack
ctx.restore();  // Pop state from stack
```

**How context works:**
- The context object (ctx) is your "brush" for drawing
- All drawing operations are performed through this context
- The context maintains a state stack including:
  - Current transformation matrix
  - Current clipping region
  - Current values of attributes like strokeStyle, fillStyle, etc.
- `save()` captures the complete state of the canvas
- `restore()` reverts to the last saved state
- States can be nested (multiple saves)

## Transformations

Transform operations affect all subsequent drawing operations.

```javascript
// Scale
ctx.scale(2, 2); // Double size in both dimensions

// Rotate (in radians)
ctx.rotate(Math.PI / 4); // 45 degrees

// Translate
ctx.translate(100, 50); // Move context

// Complex transformations
ctx.transform(m11, m12, m21, m22, dx, dy);
ctx.setTransform(m11, m12, m21, m22, dx, dy); // Reset first
```

**How transformations work:**
- `scale(x, y)`: 
  - Multiplies current scale by x and y factors
  - Affects both positions and dimensions
  - Values < 1 reduce size, > 1 increase size

- `rotate(angle)`:
  - Rotates around current origin point
  - Angle is in radians (degrees * Math.PI / 180)
  - Affects all subsequent drawing operations

- `translate(x, y)`:
  - Moves the origin point
  - Subsequent operations use new origin
  - Useful for relative positioning

- `transform()` and `setTransform()`:
  - Apply full transformation matrix
  - Parameters represent 3x3 matrix values
  - `transform()` multiplies with current matrix
  - `setTransform()` replaces current matrix

## Drawing Images

Support for drawing images, other canvases, and video frames.

```javascript
// Simple image drawing
ctx.drawImage(image, dx, dy);

// With scaling
ctx.drawImage(image, dx, dy, dw, dh);

// With clipping
ctx.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);
```

**How image drawing works:**
- Simple drawing (3 parameters):
  - `image`: Source image/canvas/video
  - `dx, dy`: Destination coordinates
  - Draws full image at specified position

- Scaled drawing (5 parameters):
  - Adds `dw, dh` for destination width/height
  - Image is scaled to fit these dimensions
  - Maintains aspect ratio if specified

- Clipped drawing (9 parameters):
  - `sx, sy, sw, sh`: Source rectangle to clip
  - `dx, dy, dw, dh`: Destination rectangle
  - Allows partial image drawing
  - Useful for sprite animations

## Compositing

Control how new shapes interact with existing content.

```javascript
// Opacity
ctx.globalAlpha = 0.5;

// Blend modes
ctx.globalCompositeOperation = 'source-over';

// Example of different blend modes
ctx.fillStyle = 'blue';
ctx.fillRect(10, 10, 50, 50);

ctx.globalCompositeOperation = 'multiply';
ctx.fillStyle = 'red';
ctx.fillRect(30, 30, 50, 50);
```

**How compositing works:**
- `globalAlpha`:
  - Controls transparency of all drawing operations
  - Ranges from 0.0 (fully transparent) to 1.0 (fully opaque)
  - Multiplies with colors' alpha values

- `globalCompositeOperation`:
  - Determines how new shapes blend with existing content
  - 'source-over' (default): New shapes drawn over existing
  - 'source-in': Shows only where both overlap
  - 'multiply': Colors multiply together
  - Affects all subsequent drawing operations

## Line Styling

Customize line appearances.

```javascript
// Basic line styling
ctx.lineWidth = 2;
ctx.lineCap = 'round';
ctx.lineJoin = 'miter';
ctx.miterLimit = 10;

// Example of different line styles
ctx.beginPath();
ctx.moveTo(10, 10);
ctx.lineTo(100, 10);
ctx.lineTo(100, 100);
ctx.stroke();
```

**How line styling works:**
- `lineWidth`: 
  - Sets thickness of lines in pixels
  - Applied centered on the actual path
  - Minimum value is 0.0 (not visible)

- `lineCap`:
  - 'butt': Ends at path endpoint
  - 'round': Adds semicircle at end
  - 'square': Adds square projecting from end

- `lineJoin`:
  - 'miter': Sharp corner (default)
  - 'round': Rounded corner
  - 'bevel': Flattened corner

- `miterLimit`:
  - Controls when miter joins convert to bevel
  - Prevents extremely sharp corners from extending too far

## Colors and Styles

Rich options for filling and stroking shapes.

```javascript
// Solid colors
ctx.strokeStyle = '#ff0000';
ctx.fillStyle = 'rgba(0, 0, 255, 0.5)';

// Linear gradient
const gradient = ctx.createLinearGradient(0, 0, 200, 0);
gradient.addColorStop(0, 'red');
gradient.addColorStop(1, 'blue');
ctx.fillStyle = gradient;

// Radial gradient
const radialGradient = ctx.createRadialGradient(100, 100, 0, 100, 100, 50);
radialGradient.addColorStop(0, 'white');
radialGradient.addColorStop(1, 'black');
ctx.fillStyle = radialGradient;

// Patterns
const pattern = ctx.createPattern(image, 'repeat');
ctx.fillStyle = pattern;

// Shadows
ctx.shadowColor = 'rgba(0, 0, 0, 0.5)';
ctx.shadowBlur = 5;
ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
```

**How colors and styles work:**
- Solid colors:
  - Accept any CSS color format
  - Hex, RGB, RGBA, HSL, named colors
  - Applied to both fills and strokes

- Linear gradients:
  - Created between two points
  - Multiple color stops allowed
  - Color stops range from 0.0 to 1.0
  - Smooth interpolation between stops

- Radial gradients:
  - Created between two circles
  - Inner and outer radius can differ
  - Can create spotlight effects
  - Perfect for circular highlights

- Patterns:
  - Repeat modes: 'repeat', 'repeat-x', 'repeat-y', 'no-repeat'
  - Can use images, other canvases
  - Useful for textures and backgrounds

- Shadows:
  - Applied to all shapes and text
  - Blur radius affects shadow softness
  - Offset creates direction and depth
  - Color can include transparency

## Path Operations

Create complex shapes using paths.

```javascript
// Basic path
ctx.beginPath();
ctx.moveTo(100, 100);
ctx.lineTo(200, 200);
ctx.closePath();
ctx.stroke();

// Curves
ctx.beginPath();
ctx.moveTo(50, 50);
ctx.quadraticCurveTo(100, 25, 150, 50);
ctx.bezierCurveTo(200, 25, 250, 75, 300, 50);
ctx.stroke();

// Arcs
ctx.beginPath();
ctx.arc(100, 100, 50, 0, Math.PI * 2);
ctx.stroke();

ctx.beginPath();
ctx.moveTo(200, 200);
ctx.arcTo(250, 200, 250, 250, 25);
ctx.stroke();
```

**How paths work:**
- Basic path operations:
  - `beginPath()`: Starts new path, clears existing
  - `moveTo()`: Moves "pen" without drawing
  - `lineTo()`: Draws straight line to point
  - `closePath()`: Connects end to start
  - `stroke()`/`fill()`: Renders the path

- Curves:
  - Quadratic curves:
    - One control point
    - Simpler, smoother curves
    - Good for simple animations
  
  - Bezier curves:
    - Two control points
    - More complex curves
    - Better for detailed shapes

- Arcs:
  - `arc()`: Creates circular arcs/circles
    - Control start/end angles
    - Optional anticlockwise direction
  
  - `arcTo()`: Creates arc between points
    - Automatically calculated tangent
    - Useful for rounded corners

## Rectangles

Efficient rectangle drawing operations.

```javascript
// Clear area
ctx.clearRect(x, y, width, height);

// Filled rectangle
ctx.fillRect(x, y, width, height);

// Outlined rectangle
ctx.strokeRect(x, y, width, height);

// Complex rectangle operations
ctx.fillStyle = 'blue';
ctx.fillRect(10, 10, 100, 100);
ctx.clearRect(20, 20, 60, 60);
ctx.strokeRect(30, 30, 40, 40);
```

**How rectangles work:**
- `clearRect()`:
  - Makes rectangle fully transparent
  - Useful for erasing areas
  - Faster than drawing white rectangle

- `fillRect()`:
  - Creates solid rectangle
  - Uses current fillStyle
  - Single operation (efficient)

- `strokeRect()`:
  - Creates outlined rectangle
  - Uses current strokeStyle and lineWidth
  - Stroke centered on path

## Pixel Manipulation

Direct access to pixel data.

```javascript
// Get pixel data
const imageData = ctx.getImageData(sx, sy, sw, sh);

// Create new pixel data
const newImageData = ctx.createImageData(width, height);

// Modify pixels
for (let i = 0; i < imageData.data.length; i += 4) {
    imageData.data[i]     = 255;  // Red
    imageData.data[i + 1] = 0;    // Green
    imageData.data[i + 2] = 0;    // Blue
    imageData.data[i + 3] = 255;  // Alpha
}

// Put pixel data
ctx.putImageData(imageData, dx, dy);
```

**How pixel manipulation works:**
- `getImageData()`:
  - Returns ImageData object
  - Contains raw pixel data
  - RGBA values (0-255)
  - Four values per pixel

- `createImageData()`:
  - Creates blank pixel array
  - All pixels initially transparent black
  - Specify dimensions or copy size

- Pixel array structure:
  - Sequential RGBA values
  - data[i]   : Red (0-255)
  - data[i+1] : Green (0-255)
  - data[i+2] : Blue (0-255)
  - data[i+3] : Alpha (0-255)

- `putImageData()`:
  - Places modified pixels back on canvas
  - Can specify dirty rectangle
  - No scaling or transformation applied

## Text Operations

Text rendering and measurement.

```javascript
// Text styling
ctx.font = '16px Arial';
ctx.textAlign = 'center';
ctx.textBaseline = 'middle';

// Drawing text
ctx.fillText('Hello World', x, y, maxWidth);
ctx.strokeText('Hello World', x, y, maxWidth);

// Measure text
const metrics = ctx.measureText('Hello World');
console.log('Text width:', metrics.width);

// Complex text example
ctx.font = 'bold 48px Arial';
ctx.textAlign = 'center';
ctx.fillStyle = 'blue';
ctx.fillText('Hello', 100, 100);
ctx.strokeStyle = 'red';
ctx.strokeText('World', 100, 150);
```

**How text operations work:**
- Text styling:
  - `font`: CSS font shorthand
  - `textAlign`: Horizontal alignment
  - `textBaseline`: Vertical alignment
  - Affects all subsequent text operations

- Text rendering:
  - `fillText()`: Solid text using fillStyle
  - `strokeText()`: Outlined text using strokeStyle
  - Optional maxWidth parameter
  - Coordinates mark text anchor point

- Text measurement:
  - `measureText()`: Returns TextMetrics object
  - Contains width and other metrics
  - Useful for layout calculations
  - Based on current font settings

## Best Practices

1. **Performance Optimization:**
   ```javascript
   // Bad
   for (let i = 0; i < 1000; i++) {
       ctx.save();
       ctx.translate(x, y);
       ctx.rotate(angle);
       ctx.drawImage(sprite, 0, 0);
       ctx.restore();
   }

   // Good
   ctx.save();
   for (let i = 0; i < 1000; i++) {
       ctx.translate(x, y);
       ctx.rotate(angle);
       ctx.drawImage(sprite, 0, 0);
       ctx.setTransform(1, 0, 0, 1, 0, 0);
   }
   ctx.restore();
   ```

2. **Memory Management:**
   ```javascript
   // Bad
   function animate() {
       const gradient = ctx.createLinearGradient(0, 0, 100, 0);
       // ... gradient setup ...
       requestAnimationFrame(animate);
   }

   // Good
   const gradient = ctx.createLinearGradient(0, 0, 100, 0);
   function animate() {
       // ... use existing gradient ...
       requestAnimationFrame(animate);
   }
   ```

3. **Responsive Design:**
   ```javascript
   function resizeCanvas() {
       const scale = window.devicePixelRatio;
   3. **Responsive Design (continued):**
   ```javascript
   function resizeCanvas() {
       const scale = window.devicePixelRatio;
       
       // Set display size
       canvas.style.width = window.innerWidth + 'px';
       canvas.style.height = window.innerHeight + 'px';
       
       // Set actual size in memory
       canvas.width = window.innerWidth * scale;
       canvas.height = window.innerHeight * scale;
       
       // Normalize coordinate system
       ctx.scale(scale, scale);
   }

   window.addEventListener('resize', resizeCanvas);
   resizeCanvas();
   ```

**How responsive design works:**
- Handles different screen sizes and pixel ratios
- Maintains sharp rendering on high-DPI displays
- Automatically adjusts when window is resized
- Scales content proportionally

4. **Animation Best Practices:**
   ```javascript
   let lastTime = 0;
   const objects = [];

   function animate(currentTime) {
       // Calculate delta time
       const deltaTime = currentTime - lastTime;
       lastTime = currentTime;

       // Clear canvas
       ctx.clearRect(0, 0, canvas.width, canvas.height);

       // Update and draw objects
       objects.forEach(obj => {
           obj.update(deltaTime);
           obj.draw(ctx);
       });

       // Request next frame
       requestAnimationFrame(animate);
   }

   // Start animation
   requestAnimationFrame(animate);
   ```

**How animation optimization works:**
- Uses requestAnimationFrame for smooth animation
- Implements delta time for consistent speed
- Batches drawing operations
- Maintains separate update and draw cycles

5. **Event Handling:**
   ```javascript
   // Convert mouse coordinates to canvas space
   function getCanvasCoordinates(event) {
       const rect = canvas.getBoundingClientRect();
       const scaleX = canvas.width / rect.width;
       const scaleY = canvas.height / rect.height;
       
       return {
           x: (event.clientX - rect.left) * scaleX,
           y: (event.clientY - rect.top) * scaleY
       };
   }

   // Handle mouse events
   canvas.addEventListener('mousedown', (event) => {
       const coords = getCanvasCoordinates(event);
       // Handle interaction
   });

   // Handle touch events
   canvas.addEventListener('touchstart', (event) => {
       event.preventDefault(); // Prevent scrolling
       const touch = event.touches[0];
       const coords = getCanvasCoordinates(touch);
       // Handle interaction
   });
   ```

**How event handling works:**
- Converts screen coordinates to canvas coordinates
- Accounts for canvas scaling and positioning
- Handles both mouse and touch inputs
- Prevents default behaviors when needed

6. **State Management:**
   ```javascript
   class CanvasState {
       constructor(ctx) {
           this.ctx = ctx;
           this.states = [];
       }

       save() {
           const state = {
               transform: this.ctx.getTransform(),
               strokeStyle: this.ctx.strokeStyle,
               fillStyle: this.ctx.fillStyle,
               lineWidth: this.ctx.lineWidth,
               // ... other properties
           };
           this.states.push(state);
       }

       restore() {
           const state = this.states.pop();
           if (state) {
               this.ctx.setTransform(state.transform);
               this.ctx.strokeStyle = state.strokeStyle;
               this.ctx.fillStyle = state.fillStyle;
               this.ctx.lineWidth = state.lineWidth;
               // ... restore other properties
           }
       }
   }
   ```

**How state management works:**
- Tracks canvas context properties
- Allows custom state saving/restoration
- Optimizes performance for complex operations
- Maintains drawing consistency

7. **Advanced Techniques:**

   ```javascript
   // Double buffering
   const backBuffer = document.createElement('canvas');
   const backCtx = backBuffer.getContext('2d');

   function render() {
       // Draw to back buffer
       backCtx.clearRect(0, 0, width, height);
       drawScene(backCtx);
       
       // Copy to main canvas
       ctx.clearRect(0, 0, width, height);
       ctx.drawImage(backBuffer, 0, 0);
   }

   // Cached rendering
   const cachedPattern = (() => {
       const patternCanvas = document.createElement('canvas');
       const patternCtx = patternCanvas.getContext('2d');
       // Draw complex pattern once
       drawComplexPattern(patternCtx);
       return patternCanvas;
   })();
   ```

**How advanced techniques work:**
- Double buffering prevents flickering
- Pattern caching improves performance
- Reduces redundant drawing operations
- Handles complex animations smoothly

## Common Gotchas and Solutions

1. **Blurry Lines:**
   ```javascript
   // Problem
   ctx.moveTo(10, 10);
   ctx.lineTo(100, 10);

   // Solution
   ctx.moveTo(10.5, 10.5);
   ctx.lineTo(100.5, 10.5);
   ```

2. **Image Loading:**
   ```javascript
   const img = new Image();
   img.onload = () => {
       ctx.drawImage(img, 0, 0);
   };
   img.src = 'path/to/image.png';
   ```

3. **Canvas Reset:**
   ```javascript
   // Reset transform and clear
   ctx.setTransform(1, 0, 0, 1, 0, 0);
   ctx.clearRect(0, 0, canvas.width, canvas.height);
   ```

## Performance Tips

1. **Batch Operations:**
   ```javascript
   // Bad
   shapes.forEach(shape => {
       ctx.beginPath();
       ctx.fillStyle = shape.color;
       ctx.fill();
   });

   // Good
   const shapesByColor = shapes.reduce((acc, shape) => {
       (acc[shape.color] = acc[shape.color] || []).push(shape);
       return acc;
   }, {});

   Object.entries(shapesByColor).forEach(([color, shapes]) => {
       ctx.beginPath();
       ctx.fillStyle = color;
       shapes.forEach(shape => {
           // Draw shape
       });
       ctx.fill();
   });
   ```

2. **Use Appropriate Data Structures:**
   ```javascript
   // For frequent lookups
   const objectsMap = new Map();
   
   // For spatial queries
   class QuadTree {
       // Implementation
   }
   ```

## Browser Support and Fallbacks

```javascript
if (canvas.getContext) {
    // Canvas is supported
    const ctx = canvas.getContext('2d');
    // Canvas code here
} else {
    // Fallback content
    const fallback = document.createElement('div');
    fallback.textContent = 'Canvas is not supported in your browser';
    canvas.parentNode.replaceChild(fallback, canvas);
}
```

## Additional Resources

1. **Documentation:**
   - [MDN Canvas API](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)
   - [HTML Living Standard](https://html.spec.whatwg.org/multipage/canvas.html)
   - [Canvas Deep Dive](https://joshondesign.com/p/books/canvasdeepdive/title.html)

2. **Tools:**
   - Performance Profilers
   - Canvas Debugging Tools
   - Asset Creation Tools

3. **Related Technologies:**
   - WebGL for 3D graphics
   - SVG for vector graphics
   - CSS animations for simple animations

## License

This cheat sheet is based on the HTML Canvas specification from WHATWG (www.whatwg.org/specs/web-apps/current-work/).

---

*Note: This document is regularly maintained and updated. For the most current information, always refer to the official documentation.*
