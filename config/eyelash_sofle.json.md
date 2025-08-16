# Eyelash Sofle Layout JSON Schema

This document describes the structure of `eyelash_sofle.json` for parsing keyboard layout data for HTML/CSS display generation.

## Root Structure

```json
{
  "id": "eyelash_sofle",
  "name": "Eyelash Sofle", 
  "layouts": { /* layout definitions */ },
  "sensors": [ /* sensor definitions */ ]
}
```

## Layout Object Structure

Each layout contains an array of key objects defining physical key positions:

```json
"layouts": {
  "default_layout": {
    "name": "default_layout",
    "layout": [ /* array of key objects */ ]
  }
}
```

## Key Object Properties

Each key in the layout array has the following properties:

| Property | Type | Units | Description | Example |
|----------|------|-------|-------------|---------|
| `row` | integer | - | Matrix row position (0-4) | `4` |
| `col` | integer | - | Matrix column position (0-13) | `9` |
| `x` | number | key units | Horizontal position from origin | `11.5` |
| `y` | number | key units | Vertical position from origin | `4.25` |
| `label` | string | - | Display label (row\ncol format) | `"4\n9"` |
| `r` | number | degrees | Rotation angle (optional) | `-30` |
| `rx` | number | key units | Rotation center X (optional) | `13` |
| `ry` | number | key units | Rotation center Y (optional) | `4.25` |

### Label Format

The `label` property uses `\n` (newline) to display matrix coordinates as two lines:
- `"4\n1"` displays as row 4 above column 1
- Labels show matrix position for debugging, not actual key functions
- Convert `\n` to `<br>` or CSS line breaks for HTML display

### Coordinate System

- **Origin**: Top-left (0,0)
- **Units**: 1 key unit H 19.05mm (0.75 inch)
- **X-axis**: Left to right (increases rightward)
- **Y-axis**: Top to bottom (increases downward)

### Rotation Properties

Keys with `r`, `rx`, `ry` properties are rotated around point (`rx`, `ry`):

- Positive rotation: Clockwise
- Negative rotation: Counter-clockwise
- Used primarily for thumb cluster keys

## Parsing for HTML/CSS Display

### Basic CSS Positioning

```css
.key {
  position: absolute;
  width: 1em;  /* 1 key unit */
  height: 1em; /* 1 key unit */
}

.key-4-9 {
  left: 11.5em;  /* x value */
  top: 4.25em;   /* y value */
  transform: rotate(-30deg); /* r value */
  transform-origin: 1.5em 0em; /* rx-x, ry-y relative to key */
}
```

### JavaScript Parsing Example

```javascript
function parseKeyLayout(layoutData) {
  return layoutData.layouts.default_layout.layout.map(key => ({
    id: `key-${key.row}-${key.col}`,
    matrixPos: { row: key.row, col: key.col },
    position: { x: key.x, y: key.y },
    rotation: key.r || 0,
    rotationCenter: key.r ? { x: key.rx, y: key.ry } : null,
    label: key.label
  }));
}
```

### Split Keyboard Layout

The layout represents both halves with a gap between them:

- Left half: Columns 0-5 
- Gap: Column 6 (empty)
- Right half: Columns 7-13

The gap creates visual separation between split halves in the coordinate space.

## Sensors Array

Optional array defining rotary encoders or other input sensors:

```json
"sensors": [{
  "ref": "left_encoder",
  "name": "encoder_left", 
  "identifier": "encoder_left",
  "compatible": "alps,ec11",
  "label": "LEFT_ENCODER",
  "enabled": true
}]
```

For layout display purposes, sensors can typically be ignored unless you need to show encoder positions.

## Removing Keys for Barebones Build

The JSON includes keys that may not exist on barebones hardware builds. To remove a key:

### Steps to Remove a Key

1. **Identify the key** in the layout array by its `row` and `col` values
2. **Remove the key object** from the `layout` array
3. **Update matrix references** in other files if needed:
   - `boards/arm/eyelash_sofle/eyelash_sofle.keymap` - Remove corresponding keymap positions
   - `boards/arm/eyelash_sofle/eyelash_sofle.dtsi` - Update matrix transform if structure changes

### Important Considerations

- **Matrix integrity**: Removing keys doesn't require renumbering remaining keys
- **Row/Col values**: Keep existing row/col values for remaining keys unchanged
- **Coordinate gaps**: Physical gaps in the layout are normal and expected
- **ZMK Studio sync**: Changes to this JSON affect ZMK Studio's layout understanding

### Common Keys to Remove (Barebones Build)

Based on barebones Sofle builds, commonly missing keys include:
- Rotary encoder positions
- OLED display mounting keys  
- Extra thumb cluster keys
- Function row extensions

### Example Removal

To remove key at row 4, col 0 (encoder position):

```json
// REMOVE this object from the layout array:
{ "row": 4, "col": 0, "x": 8, "y": 3.5, "label": "4\n0" }
```

The matrix positions in your keymap file should correspond to the remaining keys in this JSON.