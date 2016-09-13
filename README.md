# moncfg-tools

KDE doesn't seem to play nice with multiple monitors since forever. For example, if you disconnect a monitor, the system will behave as if the monitor was still plugged in, showing things in space that you can't see. Some time ago, plugging in a secondary monitor would require you to manually configure it. All of this is mildly annoying, which is why these tools were created.

## Dependencies

* x11
* xorg
* xdotool
* colors.sh (provided)
* the scripts may assume you're running KDE/plasma5

## Installation

Move scripts somewhere in your `$PATH` and you're done.

# moncfg

Is a fancy wrapper for xrandr, offering commands that are easier to remember and shorter to type. It's pretty much DE-agnostic.

## Dependencies

* x11
* xrandr
* that's pretty much it

## Limitations

This script assumes that you have 2 monitors at most. 

## Usage

Before doing anything, you should specify your default monitor by editing `defaultDisplay` line in the script.

The command:

     moncfg <monitor> <options>

<monitor> is either 'default' (defaultDisplay) or 'external' (monitor that isn't defaultDisplay).  
<options> are:

* **off** — turns the specified monitor off
* **only** — turns the other monitor off
* **[position]** — position of the specified monitor (relative to the other one). Valid positions are:
 * *left* — to the left of the other monitor
 * *right* — to the right of the other monitor
 * *up* OR *above* — above the other monitor
 * *down* OR *below* — below the other monitor
 * *same* or *dup* — both monitors show the same area.
* **flip [direction]** — mirrors the display over specified direction. Directions are:
 * *normal* — isn't mirrored.
 * *x* — flip horizontally
 * *y* — flip vertically
 * *xy* — both
* **rotate [direction]** — rotates the display in specified direction. These are:
 * *normal* or *0* — not rotated
 * *left* or *ccw* or *-90* or *270* — rotates 90° counter-clockwise
 * *inverted* or *flip* or *ud* or *180* — rotates 180°
 * *right* or *cw* or *90* — rotates 90° clock-wise
* **main** — the specified monitor is the primary monitor.
* **save** — saves specified configuration as default for this monitor (or more accurately, for this EDID. Requires `moncfg-auto`).
* **save set-default** — saves specified configuration as default for any new monitor that hasn't been configured yet. (Requires `moncfg-auto`).

# moncfg-auto

This script automatically configures your monitor for you. When `moncfg-auto` is launched, it automatically fetches the last saved configuration for external monitor with the current EDID. 

## Dependencies

* moncfg
* it assmues you're running KDE. It contains some KDE-specific hacks.

## Limitations:

It works with two monitors max.

## Usage

Before doing anything, you should specify your default monitor by editing `defaultDisplay` line in the script. You can also change where configurations get saved (again, in the script itself). If the specified directory doesn't exist, the script will create it.

This script runs forever (until terminated), watching for monitors getting plugged or unplugged. If ran with `-d`, the script will launch in the background.

# moncfg-lite

This is a simplification of the previous scripts. It takes no arguments. It contains few KDE-specific hackcs (such as restarting plasmashell and kwin_x11).

## Dependencies

* x11
* xrandr

## Limitations

This script was made specifically for KDE/plasma5. If you try to use this script in anything that isn't KDE/plasma5, you need to edit all `plasmashell` references out of the script. It also assumes that displays are arranged in a single row. This script doesn't support monitors in multiple rows.

This script also configures monitors to use their recommended resolutions. If you want a monitor to use a different resolution, you have to configure that yourself.

## Usage

Primary monitors and monitor order is hardcoded in the script. You should edit the `DEFAULT_DISPLAYS` and `DISPLAY_ORDER` variables before using the script.

`DEFAULT_DISPLAYS` is an array that defines which display is the default display. It can contain multiple monitors. First display on the list is the default display. If the first display on that list is unplugged, the next display on the list will be default. (Please note that if we're left with a single connected display, that display will be made primary regardless of whether it's actually in this list or not)

`DISPLAY_ORDER` specifies order of monitors from left to right. Outputs not listed in this array will not be configured by the script.

# mond

This script started as a way to fix the mess that happens when you connect or disconnect a monitor on KDE/plasma5. It expanded a little to cover some other issues with KDE as well. So far, this script:

* re-configures x11 when it detects a monitor was plugged/unplugged
* monitors for changes in display resolutions
* checks whether display runs at its recommended resolution
* restarts `kwin_x11` if it crashed
* restarts `plasmashell` if it crashed
* if compositing was turned off for any reason, it turns it back on. 

`mondmond` monitors for `mond` crashes.

`mond` and `mondmond` log their actions. Logfiles are `/tmp/mond.log` and `/tmp/mondmond.log`. 

## Dependencies

* colors.sh
* xdotool
* moncfg-lite
* mond-suspend
* mond-resume
* KDE/plasma5
* mondmond (optional)

## Limitations:

Like `moncfg-lite`, it assumes all monitors are arranged in a single row. It also forces monitors to forever stay on their recommended resolutions, which can be a problem in some cases.

## Usage

`mond` is supposed to be run with no arguments. It can be run at startup. Sometimes `mond` seems to crash for no apparent reason; this can be solved by using `mondmond` (which restarts `mond` if it detects `mond` was terminated). 

`mond` can be paused or resumed with `mond-suspend` and `mond-resume`. Pausing is convenient if you want to turn compositing off (otherwise `mond` will turn it back on as soon as it notices it went off) or run your displays at resolutions other than recommended.
