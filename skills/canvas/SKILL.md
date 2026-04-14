---
name: canvas
description: >
  Visual layer of the wiki. Add images, text cards, PDFs, and wiki pages to Obsidian canvas
  files with auto-positioning inside zones. Create and manage canvases in the vault.
---

# canvas: Visual Reference Layer

A canvas is a JSON file (`.canvas`) that Obsidian renders as an infinite visual board. This skill reads and writes canvas JSON directly.

**Read `references/canvas-spec.md` before making any canvas edits.** It is the single source of truth for the coordinate system, node types, color codes, image sizing table, and auto-positioning algorithm.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

All paths below are relative to the vault root. Canvases live in `canvases/`. Images and attachments go in `assets/`.

---

## Default Canvas

`canvases/main.canvas`

If it does not exist, create it:

```json
{
  "nodes": [
    {
      "id": "title",
      "type": "text",
      "text": "# Visual Reference\n\nDrop images, PDFs, and notes here.",
      "x": -400, "y": -300, "width": 400, "height": 120, "color": "6"
    },
    {
      "id": "zone-default",
      "type": "group",
      "label": "General",
      "x": -400, "y": -140, "width": 800, "height": 400, "color": "4"
    }
  ],
  "edges": []
}
```

---

## Operations

### status (no args: `$ARGUMENTS` is empty)

1. Check if `canvases/main.canvas` exists.
2. If yes: read it, count nodes by type, list all group node labels (zone names).
   Report: "Canvas has N nodes: X images, Y text cards, Z wiki pages. Zones: [list]"
3. If no: create it with the starter structure above.
   Report: "Created main.canvas with a General zone."
4. Tell user: "Open `canvases/main.canvas` in Obsidian to view."

---

### new (`$ARGUMENTS` = `new [name]`)

1. Slugify the name: lowercase, spaces to hyphens, strip special chars.
2. Create `canvases/[slug].canvas` with the starter structure, title updated to `# [Name]`.
3. Report: "Created canvases/[slug].canvas"

---

### add image (`$ARGUMENTS` = `add image [path or url]`)

**Resolve the image:**
- If URL (starts with `http`): download with `curl -sL [url] -o assets/[filename]`.
  Derive filename from URL path, or use `img-[timestamp].ext` if unclear.
- If local path outside vault: `cp [path] assets/`
- If already vault-relative: use as-is.

Create `assets/` if it doesn't exist.

**Detect aspect ratio:**
Use `python3 -c "from PIL import Image; img=Image.open('[path]'); print(img.width, img.height)"` or `identify -format '%w %h' [path]`.

See `references/canvas-spec.md` for the full aspect ratio to canvas size mapping table. The spec is the single source of truth for sizing — do not hardcode ratios here.

**Position using auto-layout** (see Auto-Positioning section below).

**Append node to canvas JSON and write.**

Report: "Added [filename] to [zone] zone at position ([x], [y])."

---

### add text (`$ARGUMENTS` = `add text [content]`)

Create a text node:
```json
{
  "id": "text-[slug]-[unix-timestamp]",
  "type": "text",
  "text": "[content]",
  "x": "[auto]", "y": "[auto]",
  "width": 300, "height": 120,
  "color": "4"
}
```

Position using auto-layout. Write and report.

---

### add note (`$ARGUMENTS` = `add note [wiki-page]`)

1. Search the vault for a `.md` file matching the page name (case-insensitive, partial match ok).
2. Use the vault-relative path as the `file` field.
   - Use `"type": "file"` — `.md` files are file nodes, not link nodes.
   - `"type": "link"` is for web URLs only.
3. Create a file node: width=300, height=100.
4. Position using auto-layout.

```json
{
  "id": "note-[slug]-[unix-timestamp]",
  "type": "file",
  "file": "topics/some-concept.md",
  "x": "[auto]", "y": "[auto]",
  "width": 300, "height": 100
}
```

---

### add pdf (`$ARGUMENTS` = `add pdf [path]`)

Same flow as add image, but for PDFs. Obsidian renders PDFs natively as file nodes.
- Copy to `assets/` if outside vault.
- Fixed size: width=400, height=520.
- Report page count if determinable.

---

### zone (`$ARGUMENTS` = `zone [name] [color]`)

1. Read canvas JSON.
2. Find max_y: `max(node.y + node.height for all nodes) + 60`. Use 280 if no nodes exist (leaves room above the starter title node).
3. Create a group node:

```json
{
  "id": "zone-[slug]-[unix-timestamp]",
  "type": "group",
  "label": "[name]",
  "x": -400,
  "y": "[max_y]",
  "width": 1000,
  "height": 400,
  "color": "[color or '3']"
}
```

Valid colors: `"1"`=red, `"2"`=orange, `"3"`=yellow, `"4"`=green, `"5"`=cyan, `"6"`=purple. See `references/canvas-spec.md` for the full color reference.

Write and report.

---

### list (`$ARGUMENTS` = `list`)

1. Glob `canvases/*.canvas`.
2. For each canvas: read JSON, count nodes by type.
3. Report:

```
canvases/main.canvas           14 nodes (8 images, 3 text, 2 file, 1 group)
canvases/design-ideas.canvas   42 nodes (30 images, 4 text, 8 groups)
```

---

## Auto-Positioning Algorithm

Read `references/canvas-spec.md` for the full coordinate system and pseudocode.

Place new nodes inside zones using left-to-right flow with row wrapping:

```python
def next_position(canvas_nodes, target_zone_label, new_w, new_h):
    # Find zone group node
    zone = next((n for n in canvas_nodes
                 if n.get('type') == 'group'
                 and n.get('label') == target_zone_label), None)

    if zone is None:
        # No zone: place below all content
        max_y = max((n['y'] + n.get('height', 0) for n in canvas_nodes), default=-140)
        return -400, max_y + 60

    zx, zy = zone['x'], zone['y']
    zw, zh = zone['width'], zone['height']

    # Nodes inside this zone (not other groups)
    inside = [n for n in canvas_nodes
              if n.get('type') != 'group'
              and zx <= n['x'] < zx + zw
              and zy <= n['y'] < zy + zh]

    if not inside:
        return zx + 20, zy + 20

    rightmost_x = max(n['x'] + n.get('width', 0) for n in inside)
    next_x = rightmost_x + 40

    if next_x + new_w > zx + zw:
        # Overflow: new row
        max_row_y = max(n['y'] + n.get('height', 0) for n in inside)
        return zx + 20, max_row_y + 20

    # Same row: align to top of existing nodes
    current_row_y = min(n['y'] for n in inside)
    return next_x, current_row_y
```

---

## ID Generation

Read the canvas file and collect all existing IDs before generating a new one. Never reuse an ID.

**Pattern:** `[type]-[content-slug]-[full-unix-timestamp]`

Use the full 10-digit Unix timestamp to avoid collisions in batch operations.

Examples: `img-cover-1744032823`, `text-note-1744032845`, `zone-branding-1744032901`

If a collision is detected (ID already exists), append `-2`, `-3`, etc.

---

## Summary

1. Read `references/canvas-spec.md` before editing any canvas JSON.
2. Always read the canvas file before writing — parse existing nodes to avoid ID collisions and calculate auto-positions.
3. Use `assets/` for downloaded or copied images and PDFs.
4. Report position and zone after every add operation.
