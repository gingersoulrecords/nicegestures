# Nice Gestures - SVG Gesture Animation Library

## Overview
Nice Gestures is a collection of copy-paste SVG gesture animations. No JavaScript or Lottie files required - pure CSS animations embedded in SVG.

## Available Gestures
- `swipe-down`, `swipe-up` - Touch gestures with directional arrow
- `drag-down`, `drag-up` - Mouse cursor gestures
- `tap` - Finger tap with ripple effect
- `click` - Mouse click with ripple effect
- `pinch-out`, `pinch-in` - Two-finger zoom gestures

## Usage
1. Copy any gesture SVG from https://nicegestures.gingersoul.co
2. Paste directly into HTML
3. Customize with CSS variables:

```html
<!-- The SVG inherits color from its parent -->
<div style="color: #3b82f6">
  <!-- paste gesture SVG here -->
</div>

<!-- Or use CSS custom properties -->
<svg style="
  --nges-stroke: #3b82f6;
  --nges-fill: white;
  --nges-duration: 2s;
">...</svg>
```

## CSS Custom Properties

| Variable | Default | Description |
|----------|---------|-------------|
| `--nges-stroke` | `currentColor` | Stroke/outline color |
| `--nges-fill` | `white` | Fill color for hand/cursor |
| `--nges-duration` | `2.4s` | Animation loop duration |

## Features
- **Self-contained** - Each SVG includes all styles and animations
- **No dependencies** - No JavaScript, no external files
- **Accessible** - Respects `prefers-reduced-motion`
- **Themeable** - Works on any background with CSS variables
- **Scalable** - Set any size via `width`/`height` attributes

## Example

```html
<svg width="48" height="48" viewBox="0 0 240 240" style="--nges-stroke: #6366f1;">
  <!-- Full SVG content from library -->
</svg>
```

## Source
- Website: https://nicegestures.gingersoul.co
- GitHub: https://github.com/gingersoulrecords/nicegestures

## Credits
A [Ginger Soul](https://gingersoul.co) thang
