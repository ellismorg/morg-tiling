# Morg Tiling

Created by **Ellis Morgan ([@ellismorg](https://github.com/ellismorg))**.

Automatic dwindle tiling for KDE Plasma 6, with keyboard navigation, small gaps, window groups and scratchpads. Inspired by a Hyprland workflow while keeping KDE's window manager, panels and decorations.

Version 0.3.2. Tested on Plasma 6.6.6 Wayland. GPLv3 license.

## Install

Import `morg-tiling-0.3.2.kwinscript` in System Settings → Window Management → KWin Scripts, then enable **Morg Tiling**. Disable other automatic tilers first.

KDE retains existing shortcuts. Conflicts can leave an action unassigned; check System Settings → Keyboard → Shortcuts → KWin for actions beginning **Morg Tiling**. Importing the package does not configure modifier mouse dragging.

## Behaviour

New windows split the focused tile along its longer dimension. Layouts are separate for each output and desktop. Closing, minimizing and restoring windows reflows the layout. Panels and application minimum sizes are respected; windows that cannot fit temporarily float.

Defaults: 10 logical pixels between windows, 10 at the outside, and 20 around a single tile. Fullscreen and maximized windows retain their native geometry. Directional navigation includes fullscreen windows, including browser fullscreen video on another output, and skips tiles hidden behind a fullscreen window on that output.

Hover focus is enabled with zero delay while the script runs. Keyboard focus changes move the pointer into the selected window through KWin's native action. To preserve your own mouse-focus policy, set `HoverFocus=false`. To disable pointer movement, set `WarpPointer=false`.

## Animations and focus highlight

The active window has a persistent thin blue border with a completely transparent center. It follows keyboard, mouse-hover and click focus, as well as window movement and resizing, including fullscreen windows. Selecting an empty desktop hides the border. Pausing or unloading the script removes it. Set `FocusHighlight=false` in `[Script-morg-tiling]` to disable it. The earlier timed native KDE highlight has been replaced; `FocusHighlightDuration` is no longer used. No panel tint or focus flash is drawn.

Smooth movement and resizing use the optional companion **Morg Tiling Animations** desktop effect. Apps receive their final geometry once; the compositor animates its appearance with an easing curve over 180 ms. Rapid changes continue from the current visual position. Mouse dragging stays direct; panels, popups, fullscreen windows, minimization and fullscreen desktop effects are excluded. The companion also smooths other programmatic geometry changes to normal app windows, including floating windows. Disable the companion separately if you disable the tiling script.

Install the companion archive from this repository using:

```sh
kpackagetool6 --type KWin/Effect --install morg-tiling-animations-0.3.0.tar.gz
kwriteconfig6 --file kwinrc --group Plugins --key morg-tiling-animationsEnabled true
qdbus6 org.kde.KWin /Effects org.kde.kwin.Effects.loadEffect morg-tiling-animations
```

Some distributions name the D-Bus command `qdbus` instead of `qdbus6`. The effect appears as **Morg Tiling Animations** in System Settings → Desktop Effects. Use `--upgrade` instead of `--install` when updating an existing effect.

Its settings in `~/.config/kwinrc` are:

```ini
[Effect-morg-tiling-animations]
Enabled=true
Duration=180
```

Duration is milliseconds, adjusted by KDE's global animation speed. Set it to `0` or disable the effect for instant movement. After manually editing settings, run `qdbus6 org.kde.KWin /KWin org.kde.KWin.reconfigure` to reload them. Animations change rendering only; input and hover focus use the final window geometry during the short transition.

## Shortcuts

Super means Meta, usually the Windows key.

| Keys | Action |
|---|---|
| Super + arrows | Focus windows or the desktop on an adjacent empty monitor |
| Super + Shift + arrows | Swap tiles or move toward another monitor |
| Super + Alt + arrows | Move the nearest split boundary in that direction |
| Super + Minus / Equal | Shrink / grow width |
| Super + Shift + Minus / Equal | Shrink / grow height |
| Super + Alt + Space | Toggle floating |
| Super + F | Toggle fullscreen |
| Super + Alt + F | Toggle maximize |
| Super + Q | Close normally |
| Super + P | Float and pin to all desktops |
| Ctrl + Super + Backslash | Center a floating window |
| Ctrl + Super + Alt + Backslash | Float, center, size to 55% × 70% |
| Super + Alt + Backslash | Picture-in-picture placement |
| Super + Alt + Z / Super + X | Start native interactive move / resize |
| Ctrl + Super + Alt + T | Pause / resume tiling |
| Ctrl + Super + Alt + R | Recalculate the layout |
| Super + digits | Desktop 1–10 in the current group |
| Super + Alt + digits | Move to that desktop and follow |
| Ctrl + Super + digits | Select a group of ten desktops |
| Ctrl + Super + Alt + digits | Move to that group and follow |
| Ctrl + Super + Left / Right, or Super + Page Up / Down | Previous / next desktop |
| Ctrl + Super + Shift + Left / Right, or Super + Alt + Page Up / Down | Move to previous / next desktop and follow |
| Super + Comma | Toggle a window group |
| Super + Shift + Comma | Lock / unlock group membership |
| Ctrl + Alt + Tab / Ctrl + Shift + Alt + Tab | Next / previous group member |
| Super + U | Remove a member from its group |
| Super + Alt + S, or Ctrl + Super + Shift + Up | Send window to general scratchpad |
| Super + S | Show / hide general scratchpad |
| Ctrl + Super + Shift + Down | Return scratchpad window to tiling |
| Ctrl + Shift + Escape | Toggle existing btop windows |
| Super + M / D / R | Toggle existing music / communication / Todoist windows |

Scratchpads show existing windows; they do not launch apps. Common music and communication app rules are included. Disable scratchpads if you prefer ordinary application placement. Groups and scratchpads use minimized windows, not native compositor tabs or special workspaces. Unloading or pausing releases script-hidden windows.

## Configuration

Defaults can be overridden in `~/.config/kwinrc`:

```ini
[Script-morg-tiling]
InnerGap=5
OuterGap=10
SingleGap=20
ResizeStep=0.1
HoverFocus=true
FocusHighlight=true
WarpPointer=true
OpenAtPointer=true
Groups=true
Scratchpads=true
```

`FloatClasses` optionally replaces the default comma-separated floating application list. Reload the script after changes.

## Limits

- Layout ratios, groups, manual floating choices and scratchpad state are not persisted across restarts.
- Desktop switching follows KWin's native behaviour. This implementation caps requests at 25 desktops.
- Modifier mouse-wheel workspace controls are not implemented.
- Mouse-based resizing follows KWin and updates split ratios after the drag ends.
- Application IDs differ between native, Flatpak and XWayland builds; some rules may need adjustment.
- Fullscreen browser-video focus has regression tests; different browser/session combinations still need user testing.
- Tested with 58 tiling and 13 animation regression tests and 6 development installer tests. These simulate windows and do not replace live compositor testing.

## Remove

Disable Morg Tiling in KWin Scripts and Morg Tiling Animations in Desktop Effects. Windows remain open.

## License and icon

Copyright (c) 2026 Ellis Morgan (@ellismorg).

Licensed under **GNU GPL version 3 only** (`GPL-3.0-only`); see [LICENSE](LICENSE). Redistributions must preserve the applicable copyright and license notices; modified versions must identify their changes and satisfy GPLv3 source requirements.

The original SVG icon is included in `contents/icons/morg-tiling.svg`. KWin metadata uses the standard `view-grid` icon for portability.

New app windows open on the monitor under the pointer and the current desktop, including launches from Plasma Application Launcher. Existing windows, transient dialogs, pinned windows and windows assigned to multiple desktops are not relocated. Set `OpenAtPointer=false` to use KDE's normal placement. Pointer position is sampled when a new window is created.

Super + arrows navigates within the current monitor, then onto the next monitor in that direction, including its desktop when empty. Empty monitors between occupied monitors are not skipped. The pointer moves to the empty monitor's center, so launcher apps open there. Navigation does not wrap at the edge of the monitor layout. When the desktop has focus, arrows can enter a visible window; if no adjacent monitor or directional window exists, the nearest window on that monitor is selected. Minimized windows and other desktops are excluded. Empty-monitor navigation always moves the pointer, including with `WarpPointer=false`, because the pointer identifies that desktop for subsequent navigation and app placement.
