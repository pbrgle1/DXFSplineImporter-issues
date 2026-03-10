# DXF Spline Importer — UE5 Plugin

Import DXF files directly into Unreal Engine as spline actors — no Blender or Rhino intermediate step. Export splines back to DXF. Edit with built-in spline tools and randomization utilities for architectural visualization.

---

## Features

### DXF Import

- **Supported entities** — LINE, ARC, CIRCLE, LWPOLYLINE, POLYLINE, SPLINE (NURBS), and POINT
- **NURBS conversion** — full de Boor algorithm with rational weights, periodic curves, and piecewise cubic Bezier output
- **Layer filtering** — scrollable checkbox list lets you pick which DXF layers to import
- **Color retention** — ACI palette (code 62), 24-bit true color (code 420), and BYLAYER inheritance
- **OCS support** — Object Coordinate System transformation ensures circles, arcs, and polylines on tilted planes import correctly in 3D
- **Auto unit scaling** — reads `$INSUNITS` from DXF header and auto-scales (inches, mm, metres to UE centimeters)
- **Blueprint creation** — optionally create reusable Blueprint assets per curve or point
- **Import modes** — Actors Only, Assets Only, or Both
- **Layer hierarchy** — DXF layers become organized parent-child actor groups in the Outliner (supports Rhino `$`-separated sublayers)
- **Reimport with deduplication** — reimport the same DXF and actors update in place without duplication
- **Drag-and-drop** — drag `.dxf` files from Explorer into the Content Browser to import

### DXF Export

- **Export selected splines** back to DXF format via **Tools > DXF Export**
- **Point export** — actors without spline components export as DXF POINT entities
- **Full AC1015 (R2000) compliance** — exported files open cleanly in AutoCAD and Rhino without recovery dialogs
- **Coordinate conversion** — automatic left-handed (UE) to right-handed (DXF) conversion

### Spline Tools

Accessible via **Tools > Spline Tools**:

| Tool | Description |
|------|-------------|
| **Explode** | Split multi-point splines into individual segment actors |
| **Join** | Merge splines whose endpoints are within a tolerance distance into a single continuous spline (handles reversed tangents correctly) |
| **Center Pivot** | Move actor origin to the center of the spline's bounding box without moving geometry |

### Randomization Tools

Accessible via **Tools > Randomisation**:

| Tool | Description |
|------|-------------|
| **Sub-select by %** | Randomly deselect a percentage of the current selection |
| **Random Scale** | Apply random scale within min/max range per axis |
| **Random Rotation** | Apply random rotation within min/max range per axis |
| **Random Offset** | Apply random position offset within a range per axis |

All operations support **Ctrl+Z** undo.

---

## Requirements

- Unreal Engine 5.x
- No external dependencies

---

## Importing DXF Files

### From the Menu

1. **Tools > Import DXF as Splines...** (or use the **+** Quick Add toolbar button)
2. Browse to your `.dxf` file
3. Configure import options
4. Select which layers to import
5. Click **Import**

### Drag and Drop

Drag a `.dxf` file from Windows Explorer into the Content Browser. The import dialog opens with the file path and target folder pre-filled.

### Import Options

| Option | Default | Description |
|--------|---------|-------------|
| DXF File | — | Path to your `.dxf` file |
| Scale Factor | `1.0` | Coordinate multiplier (applied on top of auto unit scaling) |
| Rotation Z | `90.0°` | Pre-rotation for CAD to UE coordinate conversion |
| Asset Folder | `/Game/DXF/` | Content Browser folder for Blueprint assets |
| Retain DXF Colour | On | Apply entity colors to spline visualization |
| Import POINT entities | Off | Also import DXF POINT entities |
| Import Mode | Both | Actors Only, Assets Only, or Both |

---

## Exporting to DXF

1. Select spline actors in the viewport
2. **Tools > DXF Export > DXF Export...**
3. Choose a save location
4. Splines are exported as DXF SPLINE entities, organized by parent actor label as DXF layers

---

## Supported DXF Entities

| DXF Entity | Result | Notes |
|------------|--------|-------|
| `LINE` | 2-point linear spline | Sharp corners |
| `ARC` | Bezier arc approximation | ~90° segments via kappa method |
| `CIRCLE` | Closed Bezier circle | 4-segment approximation |
| `LWPOLYLINE` | Multi-point linear spline | 2D polyline with elevation (code 38) |
| `POLYLINE` | Multi-point spline | 3D polyline with vertex sub-records |
| `SPLINE` | Full NURBS-to-Bezier | Degree 3 via de Boor, rational/periodic support |
| `POINT` | Point actor | Position only |

### Coordinate Handling

- DXF uses a right-handed coordinate system; UE5 uses left-handed
- The plugin applies a configurable Z rotation (default +90°) and Y-axis flip
- OCS (Object Coordinate System) entities are transformed to WCS via the DXF Arbitrary Axis Algorithm
- `$INSUNITS` auto-scaling converts inches (×2.54), mm (×0.1), and metres (×100) to UE centimeters

### Color Support

- **ACI Colors** (code 62) — 256-color indexed palette
- **True Colors** (code 420) — 24-bit RGB (takes priority over ACI)
- **BYLAYER** — resolves to the layer's own color from the DXF TABLES section

---

## Troubleshooting

**No entities imported?**
- Check that the DXF contains supported entity types
- Verify the correct layers are selected in the import dialog
- Check the Output Log for messages starting with `DXFSplineImporter:`

**Splines at wrong scale?**
- The plugin auto-scales based on `$INSUNITS` — check that your DXF has the correct units set
- Adjust the Scale Factor in the import dialog for additional scaling

**Splines rotated incorrectly?**
- Default Rotation Z of 90° works for Rhino and AutoCAD
- Set to 0° if your DXF is already in UE-compatible orientation

**Colors not showing?**
- Enable **Retain DXF Colour** in the import dialog
- Colors appear on the editor spline visualization, not on spline meshes

**Points not importing?**
- Check **Import POINT entities** in the import dialog

**Circles/arcs appear flat?**
- This usually indicates an OCS issue — the plugin supports OCS transformation, so ensure your DXF file has correct extrusion normals (codes 210/220/230)

---

## Bug Reports & Feature Requests

Use the [Issues](../../issues) tab to report bugs or request features:

- [Report a Bug](../../issues/new?template=bug_report.md)
- [Request a Feature](../../issues/new?template=feature_request.md)
