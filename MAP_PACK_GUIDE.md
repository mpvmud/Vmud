# vMUD Map Pack Creation Guide

Map packs define how terrain appears on the vMUD graphical map. Each pack is a single JSON file that controls colors, characters, tile images, and overlay indicators for every terrain type. Any MUD that supports GMCP can send a map pack to vMUD and get a fully customized graphical map for their players.

vMUD works out of the box with any GMCP-enabled MUD. Without a custom pack, it uses a built-in default that renders colored tiles based on sector numbers. Creating a custom pack lets you tailor the map to your MUD's world, theme, and terrain types.

## How It Works

There are two ways vMUD gets its map pack:

**Server-sent (recommended):** Your MUD server sends the pack JSON to the client via GMCP as a `map.pack` message. This happens on login or when the player changes their style. The player doesn't need to install anything. You control the look from the server and can update it anytime without a client update.

**Local fallback:** If the server doesn't send a `map.pack` message, vMUD loads `tile.json` from its local assets folder. This gives every MUD a working map as long as the server sends `room.map` GMCP data. You can also distribute custom packs as JSON files that players drop into their vMUD assets folder.

The server-sent approach is better because you control the experience and players don't have to manage files.

## Pack Types

There are three rendering modes, set by the `type` field:

| Type | Description |
|------|-------------|
| `color` | Solid colored rectangles per terrain. Simplest, no images needed. |
| `ascii` | Colored text characters per terrain. Supports directional glyphs based on exits. |
| `sprite` | Tile images (PNG encoded as base64). Supports directional road variants. |

## Basic JSON Structure

```json
{
  "name": "My Custom Pack",
  "description": "A description of this pack",
  "type": "color",
  "player": {
    "color": "#FF00FF"
  },
  "otherPlayer": {
    "color": "#FF00FF"
  },
  "mob": {
    "color": "#FF0000"
  },
  "mobColors": {
    "s": "#FFFF00",
    "g": "#00FF00",
    "t": "#00FFFF",
    "b": "#FF4500",
    "e": "#0066FF",
    "j": "#800080",
    "a": "#FF0000",
    "w": "#FFFFFF",
    "o": "#6200EA",
    "p": "#FF00FF"
  },
  "exitColor": "#404040",
  "ghostColor": "#5C4033",
  "terrainStyles": [
    {
      "name": "inside",
      "id": 0,
      "color": "#572b00"
    }
  ]
}
```

## Field Reference

### Top-Level Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Display name shown to the player |
| `description` | No | Short description of the pack |
| `type` | Yes | Rendering mode: `color`, `ascii`, or `sprite` |
| `player` | No | Player marker config. Has `color` and optionally `char` (for ascii mode) |
| `otherPlayer` | No | Other player marker config. Same format as `player` |
| `mob` | No | Default mob marker config |
| `mobColors` | No | Mob type color overrides (see Mob Types below) |
| `exitColor` | No | Color of exit connector bars between tiles. Default `#404040` |
| `ghostColor` | No | Color for unexplored/ghost rooms. Default `#5C4033` |
| `upIndicator` | No | How to render "up" exits (see Up/Down Indicators below) |
| `downIndicator` | No | How to render "down" exits (see Up/Down Indicators below) |
| `terrainStyles` | Yes | Array of terrain definitions (see below) |

### Mob Type Characters

The server can send a string of single characters in the room data indicating what types of NPCs or players are present. The `mobColors` object maps each character to a display color on the map. You choose which characters to use and what they mean for your MUD.

The built-in packs use these characters as a reference, but you can define any single-character codes that make sense for your game:

| Character | Example Meaning |
|-----------|-----------------|
| `s` | Shopkeeper |
| `g` | Guildmaster |
| `t` | Teacher |
| `b` | Banker |
| `e` | Enchanter |
| `j` | Jeweler |
| `a` | Armorer |
| `w` | Weaponsmith |
| `o` | Normal/generic mob |
| `p` | Player |

The client renders the first character in the mob string. Characters matching a key in `mobColors` render as colored circles. The character `o` renders as a bold white "M" letter, and `p` renders as a colored circle. You can customize colors for any character codes your server sends.

## Terrain Styles

The `terrainStyles` array defines how each terrain/sector type looks on the map. Each entry needs at minimum a `name`, `id`, and `color`.

### Sector IDs

Sector IDs are integers your MUD sends in the GMCP `room.map` data to identify terrain types. You can use any numbering system you want. The `id` field in each terrain entry maps a sector number to its visual appearance.

Below are the sector IDs used by the built-in packs. Your MUD can use these same numbers, or define your own. Just make sure the `id` values in your pack match what your server sends.

| ID | Name | Description |
|----|------|-------------|
| 0 | inside | Indoor rooms, buildings |
| 1 | city | City streets, town squares |
| 2 | field | Open grassland |
| 3 | forest | Wooded areas |
| 4 | hills | Hilly terrain |
| 5 | mountain | Mountain peaks |
| 6 | water | Shallow/swimmable water |
| 7 | deep_water | Deep/unswimmable water |
| 8 | underwater | Submerged rooms |
| 9 | air | Open sky/flying rooms |
| 10 | desert | Sandy terrain |
| 11 | snow | Snow-covered ground |
| 12 | tropical | Jungle/tropical forest |
| 13 | lava | Volcanic/lava terrain |
| 14 | ice | Frozen surfaces |
| 15 | marsh | Swamp/wetland |
| 16 | dirt_road | Unpaved paths |
| 17 | cave | Underground caverns |
| 18 | road | Paved roads |

You can add your own sector types beyond these. Just assign an ID number and add a matching entry to your pack's `terrainStyles` array. For example, if your MUD has a "graveyard" sector type as ID 20, add an entry with `"id": 20` to your pack.

### Color Pack Terrain Entry

For `type: "color"` packs, each terrain just needs a color:

```json
{
  "name": "forest",
  "id": 3,
  "color": "#228B22"
}
```

### ASCII Pack Terrain Entry

For `type: "ascii"` packs, each terrain can have either a static character or directional glyphs that change based on the room's exits.

**Static character (same glyph regardless of exits):**

```json
{
  "name": "forest",
  "id": 3,
  "color": "#55FF55",
  "char": "*"
}
```

**Directional glyphs (character changes based on exits):**

```json
{
  "name": "dirt_road",
  "id": 16,
  "color": "#AA5500",
  "glyphs": {
    "nsew": "+",
    "nse": "}",
    "nsw": "{",
    "new": "+",
    "sew": "+",
    "ns": "|",
    "ew": "-",
    "ne": "\\",
    "nw": "/",
    "se": "/",
    "sw": "\\",
    "n": "|",
    "s": "|",
    "e": "-",
    "w": "-",
    "none": "o"
  }
}
```

The `glyphs` keys are exit combinations. The client checks which exits (n, s, e, w) the room has and picks the matching glyph. For example, a room with north and east exits uses the `"ne"` glyph.

When a terrain has `glyphs`, exit connector bars between tiles are automatically hidden since the glyphs already show directionality.

### Sprite Pack Terrain Entry

For `type: "sprite"` packs, each terrain includes a base64-encoded PNG image:

```json
{
  "name": "forest",
  "id": 3,
  "color": "#228B22",
  "image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUg..."
}
```

Images should be 32x32 pixels, PNG format. The `color` field is used as a fallback if the image fails to load.

**Directional tile variants (for roads):**

Road-type terrains can include different tile images for each exit combination:

```json
{
  "name": "dirt_road",
  "id": 16,
  "color": "#8B5A2B",
  "image": "data:image/png;base64,...",
  "directionalImages": {
    "none": "data:image/png;base64,...",
    "n": "data:image/png;base64,...",
    "s": "data:image/png;base64,...",
    "ns": "data:image/png;base64,...",
    "ew": "data:image/png;base64,...",
    "ne": "data:image/png;base64,...",
    "sw": "data:image/png;base64,...",
    "nsew": "data:image/png;base64,..."
  }
}
```

The client picks the directional image matching the room's exits. If no match is found, it falls back to the base `image`. For road sectors, the client only counts exits toward other road/city sectors when selecting the variant, so a road next to a forest shows as a dead end, not a junction.

## Up/Down Indicators

Rooms with up or down exits can show staircase or arrow indicators. These are configured with the `upIndicator` and `downIndicator` fields.

**Text character style (for ASCII packs):**

```json
"upIndicator": {
  "style": "char",
  "char": "^",
  "color": "#00FF00",
  "closedColor": "#8B4513"
},
"downIndicator": {
  "style": "char",
  "char": "v",
  "color": "#00FF00",
  "closedColor": "#8B4513"
}
```

**Overlay image style (for sprite/color packs):**

```json
"upIndicator": {
  "style": "overlay",
  "images": {
    "0": "data:image/png;base64,...",
    "1": "data:image/png;base64,...",
    "3": "data:image/png;base64,..."
  },
  "closedColor": "#8B4513"
},
"downIndicator": {
  "style": "overlay",
  "images": {
    "0": "data:image/png;base64,...",
    "1": "data:image/png;base64,...",
    "3": "data:image/png;base64,..."
  },
  "closedColor": "#8B4513"
}
```

The `images` object is keyed by sector ID. Each image is a 32x32 RGBA PNG (with transparency) that gets drawn on top of the terrain tile. This lets stairs match the look of each terrain type.

Open exits use the overlay image. Closed exits (doors/hatches) use `closedColor`.

## Exit Data From the Server

The server sends exit information for each room as a string of characters:

| Character | Meaning |
|-----------|---------|
| `n` `s` `e` `w` | Open exit in that direction |
| `N` `S` `E` `W` | Closed (but visible) door in that direction |
| `u` `d` | Open exit up or down |
| `U` `D` | Closed door/hatch up or down |

Hidden doors are not sent. The client renders open exits as gray connector bars, closed doors as bright green connector bars, and up/down exits using the pack's indicator config.

## GMCP Integration

### Sending the Pack (Server Side)

When a player logs in or changes their map style, send the entire JSON file as a GMCP message:

```
GMCP: map.pack {"name":"My Pack","type":"color","terrainStyles":[...]}
```

The GMCP message name is `map.pack` and the payload is the full JSON object.

### Sending Room Data (Server Side)

The map data is sent as `room.map` GMCP messages:

```
GMCP: room.map {"size":11,"px":5,"py":5,"rooms":[...]}
```

Each room in the `rooms` array:

```json
{
  "x": 3,
  "y": 4,
  "s": 2,
  "e": "nsew",
  "m": "sg",
  "f": 0,
  "v": 12345
}
```

| Field | Description |
|-------|-------------|
| `x`, `y` | Grid position |
| `s` | Sector/terrain type ID |
| `e` | Exit string (see Exit Data above) |
| `m` | Mob string (characters from Mob Types table) |
| `f` | Room flags (bit 1 = mana room, bit 2 = heal room) |
| `v` | Room vnum (optional, used for map markers) |
| `h` | Has herbs (1/0, optional) |
| `o` | Has ore (1/0, optional) |

## Creating a New Pack Step by Step

1. Start with the `tile.json` example as a base
2. Change the `name` and `description`
3. Pick your `type` (start with `color` if you just want custom colors)
4. Edit the `color` values in each `terrainStyles` entry to match your MUD's theme
5. If using `ascii` type, add `char` or `glyphs` to each terrain
6. If using `sprite` type, create 32x32 PNG tiles, convert them to base64, and add them as `image` fields
7. Save the JSON file on your server
8. Send it to the client via GMCP `map.pack` when players log in

### Converting Images to Base64

Using Python:

```python
import base64

with open("my_tile.png", "rb") as f:
    b64 = "data:image/png;base64," + base64.b64encode(f.read()).decode()
print(b64)
```

Paste the output as the `image` value in your terrain entry.

## Tips

- Start with `tile.json` as your base. It's the simplest format and just needs color values.
- Test your pack by sending it via GMCP and walking around your world. Make sure every sector type your MUD uses has a matching entry.
- If a room's sector ID has no matching entry in the pack, the client uses the first terrain entry as a fallback.
- Keep sprite pack file sizes reasonable. Each base64 PNG tile adds about 1-3KB. A full pack with 19 terrain tiles, 48 directional road variants, and stair overlays runs about 200KB, which transfers fine over GMCP.
- You don't need to implement every feature. A color-only pack with just `name`, `id`, and `color` per terrain works perfectly. Add glyphs, sprites, directional roads, and stair overlays as you want them.

## Example Packs

vMUD ships with five built-in packs as reference. All are included in the repository under `assets/map_packs/` and can be used as templates:

- `tile.json` - Simple colored rectangles, good starting point (2KB)
- `ascii.json` - Text characters with directional glyphs (8KB)
- `enhanced.json` - Unicode box-drawing roads and terrain symbols (4KB)
- `vor.json` - Pixel sprite tiles with directional roads and stair overlays (192KB)
- `zelda.json` - RPG-style pixel art tiles with directional roads and stair overlays (78KB)

If you create a cool pack for your MUD, consider sharing it with the community.
