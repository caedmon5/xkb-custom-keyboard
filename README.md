# Custom Keyboard Repository README

## Overview

This repository provides files and documentation to help users create and install custom keyboard layouts on Linux systems using X11. It supports:

* Custom symbol files
* Layout registration through `evdev.xml`
* Persistent third-level chooser and compose key setup
* Troubleshooting for layout activation

The method described has been tested and refined over many years by users working with specialist character sets (e.g. IPA, Old English, Unicode diacritics).

## Repository Structure

```
.
├── symbols/
│   └── oe                        # Custom layout for Old English
├── docs/
│   ├── CUSTOM_KEYBOARDS.md      # Main guide to setting up custom keyboards
│   ├── TROUBLESHOOTING.md       # Common errors and resolutions
│   └── ARCHIVE.md               # Legacy instructions and community notes
└── README.md                    # This file
```

## Quick Start

To implement a layout exactly the same as the one I use (i.e. with characters suitable for working in early Germanic philology):

1. Copy `symbols/oe` to `/usr/share/X11/xkb/symbols/`
2. Append the layout block to `/usr/share/X11/xkb/rules/evdev.xml`
3. Run `udevadm trigger --subsystem-match=input --action=change` or reboot
4. Optional: Use GNOME settings or Tweaks to add the layout and assign third-level/compose keys (these are already defined in `oe` and probably not necessary if you are using my layout)

## Reference Commands

Compile layout:

```bash
xkbcomp -w9 -I/usr/share/X11/xkb /usr/share/X11/xkb/symbols/oe $DISPLAY
```

Apply layout:

```bash
setxkbmap -layout oe -variant basic
```

Reset input devices:

```bash
udevadm trigger --subsystem-match=input --action=change
```

## Issues

If the layout fails to load or does not appear in the input selector:

* Check the `evdev.xml` layout block placement
* Confirm variant names match in your `setxkbmap` or GUI selector
* See `docs/TROUBLESHOOTING.md` for help

## License

This repository is licensed under CC BY-NC-SA 3.0 unless otherwise noted.

---

* For advanced documentation, see `docs/CUSTOM_KEYBOARDS.md` and `docs/TROUBLESHOOTING.md`.
* A historical overview of the guide itself can be found in `docs/HISTORICAL_OVERVIEW.md`.
