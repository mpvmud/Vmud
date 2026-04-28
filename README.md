# VMud

**A modern MUD client built for players who grew up on zMUD.**

QPainter-rendered terminal, full GMCP/MSDP protocol support, native zMUD scripting, Lua 5.4 VM, live auto-mapping, and a UI that stays out of your way.

![Windows](https://img.shields.io/badge/platform-Windows-blue)
![Python](https://img.shields.io/badge/python-3.14-green)
![PySide6](https://img.shields.io/badge/UI-PySide6-orange)
![License](https://img.shields.io/badge/license-Proprietary-blue)

---

## About

VMud is a feature-rich MUD client designed for all MUD players. Connects with any MUD, uses enhanced GMCP integration for Void of Reality. Now built with PySide6, featuring pixel-perfect block character rendering, dual scripting engines (zMUD + Lua), and real-time game state panels.

---

## Features

### Terminal & Rendering
- **QPainter character grid** — pixel-perfect rendering of box-drawing and block characters (█▄▀), no QTextEdit font substitution issues
- **Full VT100 emulation** — SGR colors, cursor movement, scroll regions, save/restore
- **Split-screen mode** (Ctrl+Z) — frozen scrollback on top, live terminal on bottom, just like zMUD
- **Bundled CP437 font** (Px437 IBM Model3x Alt4) — authentic DOS-era MUD look
- **Customizable ANSI palette** — all 16 colors editable in preferences (256 is not supported at this time)
- **Timestamps** — optional per-line timestamps on prompt lines

### Protocol Support
- **GMCP** (Generic MUD Communication Protocol) — automatic handshake, package subscriptions, full message parsing
- **MSDP** (MUD Server Data Protocol) — variable reporting with table/array support
- **CHARSET negotiation** — UTF-8 priority with Latin-1/ASCII fallback
- **NAWS** — dynamic terminal size reporting on window resize
- **TTYPE** — terminal type identification

### GMCP-Powered Panels
- **Vitals** — HP/Mana/Move/XP bars with color transitions (green → yellow → red)
- **Stats** — STR, CON, DEX, WIS, INT displayed horizontally
- **Group** — party member vitals in real time
- **Equipment** — detachable floating window showing worn items with condition
- **Buffs/Affects** — active spells with color-coded duration countdown
- **Map** — tile-based terrain map from GMCP room data

### Mapping
**NOTE** This will not work on 100% of all muds and this client has never claimed to. What it can do is listed below:
- **GMCP mapper** — builds map from room.info packets, 11x11 on muds that send GMCP room.info viewport with BFS rendering
- **Screen-scraping automapper** — for MUDs without GMCP, detects rooms from text patterns in a 21x21 grid
- **18 terrain types** with distinct tile colors. You can upload map packs if your mud has more than 18 terrains.
- **Ghost rooms** — unvisited exits shown as brown markers
- **Right-click sector editing** — manually correct terrain types on the map
- **One way exits** - The Screen-Scraper will catch if there are 1 way rooms and delete links and exits that are not there.

### zMUD Scripting (Full Compatibility)
Native zMUD command interpreter — not a translator, a real runtime. Users migrating from zMUD can keep writing in familiar syntax.

**All 13 Pattern Wildcards:**

| Wildcard | Matches | Wildcard | Matches |
|----------|---------|----------|---------|
| `%1`-`%9` | Capture group | `%d` | Digits |
| `%w` | Word | `%*` | Rest of line |
| `%x` | Single non-space | `%n` | Number (neg/decimal) |
| `%a` | Alphanumeric | `%s` | Whitespace |
| `%t` | Tab | `%e` | End of line |
| `^` | Start anchor | `$` | End anchor |
| `~X` | Literal escape | | |

**Flow Control:**
`#IF` `#SWITCH` `#LOOP` `#WHILE` `#UNTIL` `#FORALL` `#BREAK` `#CONTINUE`

**Variables & Math:**
`#VAR` `#MATH` `#ARRAY` `#EVAL` `@variable` expansion everywhere

**Output:**
`#SHOW` (echo to terminal) · `#SEND` (to MUD) · `#GAG` · `#SUB` (text substitution) · `#CR`

**Timing:**
`#WAIT 2000` (pause ms) · `#WAIT {pattern}` (pause until match) · `#TIMER` · `#ALARM`

**Class System:**
`#CLASS` `#T+` `#T-` `#ENABLE` `#DISABLE` — enable/disable trigger groups by name

**Runtime Management:**
`#TRIGGER` `#UNTRIGGER` `#ALIAS` `#UNALIAS` — create and remove triggers/aliases on the fly

**Functions:**
`#FUNCTION` for user-defined functions, plus 10 built-in `%functions`:
`%replace` `%length` `%substr` `%upper` `%lower` `%pos` `%word` `%numitems` `%stripansi` `%format`

**Keybindings:**
`#KEY F1 {cast heal}` — F1-F12, Ctrl+X, Shift+X, Alt+X combos

### zMUD Import
Import your existing zMUD/CMUD export files with full support for:

`#TRIGGER` `#ACTION` `#ALIAS` `#VAR` `#KEY` `#MACRO` `#HIGHLIGHT` `#HI` `#COLOR` `#SUB` `#GAG` `#FUNCTION` `#CLASS` `#TIMER` `#ALARM` `#TAB` `#PATH` `#PROMPT` `#ONINPUT`

Complex trigger responses containing `#IF`, `#WAIT`, `#LOOP`, etc. are preserved as-is and executed by the interpreter at runtime.

### Lua 5.4 Scripting
Full sandboxed Lua VM running alongside zMUD scripting — use whichever you prefer, or both.

- **Mudlet-compatible API** — `send()`, `echo()`, `cecho()`, `tempTrigger()`, `tempTimer()`, `tempAlias()`
- **Event system** — `registerAnonymousEventHandler()`, `raiseEvent()`, GMCP events
- **Per-profile scripts** — .lua files loaded automatically on connect
- **Syntax-highlighted editor** — built-in Lua editor with error output panel

### Triggers & Aliases
- **Visual editors** with tree view, grouping/folders, inline pattern tester
- **Trigger types** — send command, gag line, highlight (fg/bg color), text substitution, one-shot
- **Alias expansion** — `%1`-`%9` arguments, `%*` for all args, semicolon command chaining
- **Regex support** — full Python regex with capture groups
- **Per-profile storage** — JSON persistence, automatic save on change

### Quality of Life
- **Auto-reconnect** — 10-second countdown on disconnect, press Enter to skip
- **Password detection** — suppresses echo on password prompts, auto-sends saved passwords
- **Command history** — up/down arrows, per-profile, persists across sessions (200 entries)
- **Numpad movement** — 2/4/5/6/8 for south/west/look/east/north when input is empty
- **Command chaining** — `*` splits commands (e.g. `kick 1.*bash 1.`), disabled for chat commands
- **Spell checking** — MUD-aware dictionary with 100+ terms, toggle on/off
- **Multiple windows** — Ctrl+Shift+N launches a new VMud instance
- **Update checker** — checks GitHub releases on startup, notifies of new versions

### Profiles & Settings
- **Per-profile data** — triggers, aliases, variables, keybindings, timers, functions, command history, map data, Lua scripts
- **Quick-connect servers** — built-in server list with one-click connect
- **Customizable fonts** — separate terminal and input font settings
- **Customizable colors** — full 16-color ANSI palette editor
- **Layout persistence** — splitter positions, window size, equipment window position all saved
- **

---

## Installation

Download the latest installer from [Releases](../../releases):

```
VMud_Setup_X.X.X.exe
```

Run the installer — it installs to `C:\VMud` by default (no admin required).

---

## Building from Source

**Requirements:** Python 3.12+, PySide6, lupa (optional, for Lua scripting)

```bash
pip install PySide6 lupa pyspellchecker pyinstaller

cd vor_client

# Build executable
python -m PyInstaller VoRClient.spec --noconfirm

# Build installer (requires Inno Setup 6)
iscc installer/VMudSetup.iss
```

---

## Quick Start

1. Launch VMud
2. **Connection > Connect** (Ctrl+N)
3. Select a server or enter host:port
4. Play!

**Scripting quick start:**
- **Scripts > Triggers** (Alt+T) — create triggers with pattern matching
- **Scripts > Aliases** (Alt+A) — create command shortcuts
- **Scripts > Lua Scripts** (Alt+L) — write Lua scripts
- **Scripts > Import zMUD Triggers** — import your old zMUD settings

---

## Works to connect With Any MUD

VMud is designed to connect to any MUD server. GMCP-enabled MUDs get the full experience (vitals, map, group, equipment panels). Non-GMCP MUDs still get triggers, aliases, scripting, automapper, and everything else.

**Built-in server list:**
- Void of Reality
- Rites of Passage 2
- Everwar Redux
- Everwar Ascendency
- Fate
- Aturion Dynasty

---

## License

All Rights Researved. Built with love for the MUD community.

# vMUD EULA

By downloading, installing, or using vMUD you agree to the following.

## What you can do:
- Use vMUD for free on as many computers as you want
- Connect to any MUD server with it
- Create and share map packs, triggers, aliases, and scripts
- Stream, record, or screenshot vMUD in use
- Talk about it, review it, recommend it or trash it

## What you can't do:
- Sell vMUD or any modified version of it
- Reverse engineer, decompile, or disassemble the binary
- Access, copy, distribute, or publish any source code of vMUD without permission
- Bundle it with other software without my permission
- Use it to attack, disrupt, or interfere with any MUD server

## Termination
If you violate these terms, your right to use vMUD is automatically revoked.

## No warranty
vMUD is provided as-is. I built it because I wanted a better MUD client and decided to share it. 
I do my best to keep it working and fix bugs but I make no guarantees. If something breaks, let 
me know and I will try to fix it. I am not responsible for any lost data, crashed MUD sessions, 
or anything else that goes wrong.

## Third-party stuff
vMUD uses open source libraries listed in LICENSES.md. Those libraries have their own licenses 
which still apply. The fonts, icons, and tile art bundled with vMUD are either original work or
used under their respective licenses as documented.

vMUD may integrate with third-party services such as Discord Rich Presence.
This feature only updates your activity status and does not access personal
messages, account data, or authentication tokens.

## I can update this
I might update this EULA as the project grows. Continued use of vMUD after changes means you accept
the updated terms. I'll note any changes in the release notes.

Copyright 2026 - Kilith
