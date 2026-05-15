<div align="center">

```
██╗   ██╗██╗    ██╗██╗    ██╗███╗   ███╗
██║   ██║██║    ██║██║    ██║████╗ ████║
██║   ██║██║ █╗ ██║██║ █╗ ██║██╔████╔██║
╚██╗ ██╔╝██║███╗██║██║███╗██║██║╚██╔╝██║
 ╚████╔╝ ╚███╔███╔╝╚███╔███╔╝ ██║ ╚═╝ ██║
  ╚═══╝   ╚══╝╚══╝  ╚══╝╚══╝  ╚═╝     ╚═╝
```

**Versatile Wayland Window Manager**

*A dwl-based tiling compositor with an infinite canvas, directional focus, and a keyboard-driven workflow.*

---

![License](https://img.shields.io/badge/license-GPL--3.0-blue?style=flat-square)
![Language](https://img.shields.io/badge/language-C-informational?style=flat-square)
![Wayland](https://img.shields.io/badge/wayland-compositor-orange?style=flat-square)
![wlroots](https://img.shields.io/badge/wlroots-0.19-blueviolet?style=flat-square)
![Version](https://img.shields.io/badge/version-0.8--dev-green?style=flat-square)

</div>

---

## What is vwwm?

**vwwm** is a Wayland compositor / tiling window manager forked from [dwl](https://codeberg.org/dwl/dwl). It keeps the suckless philosophy intact — everything is configured at compile-time via `config.h` — while layering several powerful additions on top:

- 🗺️ **Infinite canvas** per tag — pan across an unbounded workspace in floating layout
- 🧭 **Directional focus & move** — navigate and reorder windows spatially with arrow keys
- 🪟 **Configurable gaps** between tiled windows
- 🏷️ **9 tags** (virtual desktops), each with its own independent canvas state
- 🖥️ **Multi-monitor** support with per-monitor rules and transforms
- 🔒 **Session locking** via `ext-session-lock-v1`
- 🖱️ **Rich trackpad support** — tap-to-click, natural scroll, drag lock, and more
- ⌨️ **XWayland support** (optional, compile-time flag)

---

## Feature Highlights

### 🗺️ Infinite Canvas
In floating layout each tag gets an **infinite canvas**: a coordinate space larger than your screen. Pan the viewport with keyboard shortcuts (`Alt+Shift+Arrows`) or drag with the mouse (`Alt+Shift+Left Click`). Jump back to the origin any time with `Alt+R`.

Each tag remembers its own canvas offset independently — switching tags seamlessly saves and restores the pan position.

### 🧭 Directional Navigation
Focus the nearest window in any cardinal direction with `Alt+Ctrl+Arrows`. In tiling mode, `Alt+Ctrl+Shift+Arrows` **moves** a window relative to its neighbors by reordering the client list. In floating mode the same binding nudges the window by a fixed pixel step.

### 🏗️ Layouts
| Symbol | Name     | Description                               |
|--------|----------|-------------------------------------------|
| `[]=`  | Tile     | Classic master/stack tiling with gaps     |
| `><>`  | Float    | Free-floating with infinite canvas        |
| `[M]`  | Monocle  | Maximized single window, count in symbol  |

Switch layouts on the fly — vwwm preserves canvas positions when toggling between float and tile.

### 📡 Wayland Protocol Coverage
vwwm implements an extensive set of Wayland protocols:

| Protocol | Purpose |
|---|---|
| `xdg-shell` v6 | Application windows |
| `wlr-layer-shell` v3 | Panels, bars, overlays |
| `ext-session-lock-v1` | Screen locking |
| `wp-cursor-shape-v1` | Themed cursor shapes |
| `wp-fractional-scale-v1` | HiDPI fractional scaling |
| `xdg-decoration-unstable-v1` | Server-side decorations |
| `pointer-constraints-v1` | Mouse confinement (games) |
| `relative-pointer-v1` | FPS mouse input |
| `wlr-output-power-management` | Display sleep/wake |
| `wlr-virtual-pointer` | Virtual pointer devices |
| `linux-drm-syncobj-v1` | GPU explicit sync |
| `alpha-modifier-v1` | Per-surface transparency |
| `wlr-screencopy-v1` | Screenshot tools |
| `wlr-data-control-v1` | Clipboard managers |
| `ext-data-control-v1` | Extended clipboard control |

---

## Dependencies

| Dependency | Version  |
|---|---|
| **wlroots** | `0.19.x` |
| **wayland-server** | latest   |
| **libinput** | any      |
| **xkbcommon** | any      |
| **wayland-protocols** | any      |
| xcb, xcb-icccm | *XWayland only* |

---

## Building

```sh
git clone https://github.com/Skartorion/vwwm
cd vwwm/vwwm
cp config.def.h config.h   # copy default config — edit to taste
make
sudo make install
```

To enable **XWayland** support, uncomment the relevant lines in `config.mk`:

```makefile
XWAYLAND = -DXWAYLAND
XLIBS    = xcb xcb-icccm
```

Then rebuild:

```sh
make clean && make
```

---

## Configuration

All configuration lives in `config.h` (generated from `config.def.h`). Recompile after editing.

### Appearance

```c
static const unsigned int borderpx = 1;    /* border width in pixels */
static const unsigned int gappx    = 5;    /* gap between tiled windows */
static const int sloppyfocus       = 1;    /* focus follows mouse */

static const float rootcolor[]    = COLOR(0x222222ff);
static const float bordercolor[]  = COLOR(0x444444ff);
static const float focuscolor[]   = COLOR(0x005577ff);
static const float urgentcolor[]  = COLOR(0xff0000ff);
```

### Infinite Canvas

```c
static const int canvasstep = 120;  /* pixels per canvas pan keypress */
```

### Window Rules

```c
static const Rule rules[] = {
    /* app_id        title   tags mask   isfloating  monitor */
    { "firefox",     NULL,   1 << 8,     0,          -1 },  /* tag 9 */
    { "mpv",         NULL,   0,          1,          -1 },  /* always float */
};
```

---

## Default Keybindings

> **MODKEY = `Alt`**

### Windows

| Keybinding | Action |
|---|---|
| `Mod + Shift + Return` | Spawn terminal (`foot`) |
| `Mod + p` | Open app launcher (`wmenu-run`) |
| `Mod + Shift + c` | Kill focused window |
| `Mod + j / k` | Focus next / prev in stack |
| `Mod + Return` | Zoom (promote to master) |
| `Mod + Shift + Space` | Toggle floating |
| `Mod + e` | Toggle fullscreen |
| `Mod + Shift + d` | Center window on canvas |

### Directional Focus & Move

| Keybinding | Action |
|---|---|
| `Mod + Ctrl + ←/→/↑/↓` | Focus nearest window in direction |
| `Mod + Ctrl + Shift + ←/→/↑/↓` | Move window in direction |

### Infinite Canvas (floating layout)

| Keybinding | Action |
|---|---|
| `Mod + Shift + ←/→/↑/↓` | Pan canvas in direction |
| `Mod + Shift + Left Click drag` | Pan canvas with mouse |
| `Mod + r` | Reset canvas to origin |

### Layouts

| Keybinding | Action |
|---|---|
| `Mod + t` | Tiling layout |
| `Mod + f` | Floating layout |
| `Mod + m` | Monocle layout |
| `Mod + Space` | Toggle last two layouts |
| `Mod + h / l` | Adjust master width factor |
| `Mod + i / d` | Increase / decrease master count |

### Tags & Monitors

| Keybinding | Action |
|---|---|
| `Mod + 1–9` | Switch to tag |
| `Mod + Shift + 1–9` | Move window to tag |
| `Mod + Ctrl + 1–9` | Toggle tag view |
| `Mod + 0` | View all tags |
| `Mod + , / .` | Focus left / right monitor |
| `Mod + Shift + Q` | Quit vwwm |

### Mouse Buttons (hold Mod)

| Button | Action |
|---|---|
| `Left Click` | Move window |
| `Right Click` | Resize window |
| `Middle Click` | Toggle floating |

---

## Running

Start vwwm from a TTY or display manager:

```sh
# bare start
vwwm

# with a startup command
vwwm -s "waybar & foot"

# enable debug logging
vwwm -d
```

A `.desktop` file is installed to `/usr/local/share/wayland-sessions/vwwm.desktop` for display manager integration.

---

## Repository Layout

```
vwwm/
├── vwwm/           — main compositor source
│   ├── vwwm.c      — compositor core (~3000 lines)
│   ├── client.h    — client/window abstraction
│   ├── config.def.h — default configuration
│   ├── config.mk   — build flags & paths
│   ├── Makefile
│   ├── util.c/h    — utility helpers
│   ├── protocols/  — bundled Wayland XML protocols
│   └── vwwm.1      — man page
├── dwl-main/       — upstream dwl reference
└── vxwm-main/      — vxwm reference
```

---

## License

vwwm is distributed under the **GPL-3.0** license, with portions under the MIT license from dwm (`LICENSE.dwm`), sway (`LICENSE.sway`), and tinywl (`LICENSE.tinywl`).

---

<div align="center">

*Built on [dwl](https://codeberg.org/dwl/dwl) · Powered by [wlroots](https://gitlab.freedesktop.org/wlroots/wlroots)*

</div>
