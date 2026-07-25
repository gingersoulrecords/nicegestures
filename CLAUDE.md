# Nice Gestures - Development Guide

## Overview
Nice Gestures is a collection of copy-paste SVG gesture animations. No JavaScript or Lottie files required - pure CSS animations embedded in SVG.

- Website: https://nicegestures.gingersoul.co
- GitHub: https://github.com/gingersoulrecords/nicegestures

---

## Design Specifications

### SVG Structure
- **ViewBox**: All gesture SVGs use `viewBox="0 0 240 240"`
- **Icons inside**: Positioned at center using `transform="translate(120, 120) scale(5) translate(-12, -12)"` for 24x24 icons scaled to fit

### Color Palette (Tailwind Slate)
| Usage | Light Mode | Dark Mode |
|-------|------------|-----------|
| Background | `#ffffff` | `#0f172a` (slate-900) |
| Background Secondary | `#f8fafc` (slate-50) | `#1e293b` (slate-800) |
| Text | `#0f172a` (slate-900) | `#f1f5f9` (slate-100) |
| Text Muted | `#64748b` (slate-500) | `#94a3b8` (slate-400) |
| Border | `#e2e8f0` (slate-200) | `#334155` (slate-700) |

### Stroke Specifications
| Element Type | stroke-width | stroke-linecap | stroke-linejoin |
|--------------|--------------|----------------|-----------------|
| Touch gestures (hand) | `1` | `round` | `round` |
| Pointer gestures (cursor) | `1` | `round` | `round` |
| Scroll gestures (mouse) | `1.5` | `round` | `round` |

---

## CSS Custom Properties

### Public API (user-facing)
```css
--gesture-stroke    /* Stroke color (default: currentColor) */
--gesture-fill      /* Fill color (default: white, dark: #0f172a) */
--gesture-duration  /* Animation duration (default: 2.4s) */
```

### Internal Variables (inside each SVG)
Each template maps public vars to internal shorthand:
```css
.gesture-[name] {
  --_d: var(--gesture-duration, 2.4s);  /* duration */
  --_s: var(--gesture-stroke, currentColor);  /* stroke */
  --_f: var(--gesture-fill, white);  /* fill */
}
```

### Dark Mode Fill Override
Each gesture includes a media query to change default fill in dark mode:
```css
@media (prefers-color-scheme: dark) {
  .gesture-[name] { --_f: var(--gesture-fill, #0f172a); }
}
```

---

## Available Gestures

### Pointer Gestures (cursor icon)
- `pointer-click` - Single click with ripple
- `pointer-double-click` - Double click with ripple
- `pointer-long-press` - Long press with expanding ring
- `pointer-drag-down`, `pointer-drag-up`, `pointer-drag-left`, `pointer-drag-right`

### Touch Gestures (hand icon)
- `touch-tap` - Single tap with ripple
- `touch-double-tap` - Double tap with ripple
- `touch-long-press` - Long press with expanding ring
- `touch-drag-down`, `touch-drag-up`, `touch-drag-left`, `touch-drag-right`

### Scroll Gestures (mouse icon)
- `scroll-down`, `scroll-up`

---

## Hand Icon (Touch Gestures)

### Two-Layer Approach
The hand icon uses a **two-layer system** to support fill and stroke:

1. **Fill layer** (behind): A closed path that fills the hand silhouette
2. **Stroke layer** (on top): Original Tabler stroke paths for the outline

This approach was necessary because the Tabler hand-finger icon uses open stroke paths that can't be filled directly without artifacts.

### Fill Path (from Inkscape)
The fill path was created using Inkscape's Paint Bucket tool on the original Tabler strokes:
```svg
<path class="gesture-fill" d="M 11.55,21.448476 C 10.390178,21.294325 ... z"/>
```

### Stroke Paths (from Tabler)
Original Tabler icon-tabler-hand-finger paths:
```svg
<g class="gesture-stroke">
  <path d="M8 13v-8.5a1.5 1.5 0 0 1 3 0v7.5"/>
  <path d="M11 11.5v-2a1.5 1.5 0 1 1 3 0v2.5"/>
  <path d="M14 10.5a1.5 1.5 0 0 1 3 0v1.5"/>
  <path d="M17 11.5a1.5 1.5 0 0 1 3 0v4.5a6 6 0 0 1 -6 6h-2h.208a6 6 0 0 1 -5.012 -2.7..."/>
</g>
```

### CSS for Two-Layer Hand
```css
.gesture-touch-tap .gesture-fill { fill: var(--_f); stroke: none; }
.gesture-touch-tap .gesture-stroke { fill: none; stroke: var(--_s); stroke-width: 1; stroke-linecap: round; stroke-linejoin: round; }
```

### Source Files
- `hand-finger-ink.svg` - Inkscape file with the fill path

---

## Animation Patterns

### Naming Convention
Keyframes follow the pattern: `@keyframes gesture-[gesture-name]-[element]`
- `-el` suffix: Main element animation (scale, translate)
- `-ind` suffix: Indicator animation (ripple, opacity)
- `-ring` suffix: Long-press expanding ring
- `-wheel` suffix: Scroll wheel movement

### Standard Animation Structure
```css
@media (prefers-reduced-motion: no-preference) {
  .gesture-[name] .gesture-el { animation: gesture-[name]-el var(--_d) ease-in-out infinite; }
  .gesture-[name] .gesture-indicator { animation: gesture-[name]-ind var(--_d) ease-out infinite; }
}
```

### Common Animation Timing
- **Default duration**: 2.4s
- **Tap/click**: Quick scale down at ~12%, back up by ~25%
- **Drag**: Translate movement with pause at start/end
- **Long-press**: Scale with expanding ring overlay

### Indicator (Ripple) Pattern
```css
.gesture-indicator {
  fill: var(--_s);
  opacity: 0;
  transform-origin: [center of action];
}
```

---

## Template Structure

Each gesture is defined as an HTML `<template>`:
```html
<template id="tpl-[gesture-name]">
  <svg viewBox="0 0 240 240" xmlns="http://www.w3.org/2000/svg">
    <style>
      /* CSS custom properties */
      /* Dark mode override */
      /* Element styles */
      /* Animations (with prefers-reduced-motion) */
      /* Keyframes */
    </style>
    <g class="gesture-[name]">
      <circle class="gesture-indicator" ... />
      <g class="gesture-el">
        <!-- Icon paths -->
      </g>
    </g>
  </svg>
</template>
```

---

## Toolbar Preview Sizes

Available sizes for preview in the library UI:
```javascript
const sizeToPreview = {
  16: '35%',
  20: '42%',
  24: '50%',
  32: '60%',
  48: '75%',
  96: '95%'
};
```

---

## Copy Behavior

When copying SVGs, colors are baked in based on current theme:
```javascript
const isDark = getEffectiveTheme() === 'dark';
const strokeColor = isDark ? '#f1f5f9' : '#0f172a';
const fillColor = isDark ? '#0f172a' : '#ffffff';
```

---

## Future Enhancements
- Speed control (slow/normal/fast)
- Additional gestures (pinch, rotate, multi-finger)
- Customizable indicator color
- Export format options

---

## Credits
- Hand icon based on [Tabler Icons](https://tabler.io/icons) `hand-finger`
- A [Ginger Soul](https://gingersoul.co) thang
