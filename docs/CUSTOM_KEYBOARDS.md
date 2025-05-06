# Custom Keyboard Layouts in Linux/X11

## 1. Introduction

This guide explains how to create and use a custom keyboard layout in X11-based Linux distributions, such as Ubuntu and Debian. It is based on over a decade of experience developing and maintaining such layouts, and has been used by hundreds of people for configuring keyboards to support non-standard characters and diacritics.

To implement this, you must:

* Create a  symbols file (or edit an existing one) in `/usr/share/X11/xkb/symbols`. The one I have developed for my work as an early medieval philologist and teacher of grammar and the history of English (`oe`) can be found in the `symbols/` of this repository.
* Edit  `/usr/share/X11/xkb/rules/evdev.xml` (if you have added a new file in `symbols/`) describing the file and making it visible to your system's input method selector. 

Using this method, you can create keyboards that are adapted to your daily needs: add mappings for IPA characters, letter from other languages,  accented characters, and more, to standard keys on your physical keyboard. You can also configure "third-level chooser" and "compose" keys to enable advanced character input.

## 2. Quick Start (Ubuntu ≥ 16.04)

1. Copy your keyboard layout file to `/usr/share/X11/xkb/symbols/`
2. Edit `/usr/share/X11/xkb/rules/evdev.xml` to register the layout
3. Reboot or reset XKB configuration (see §6, below)
4. Add the keyboard in Settings → Region & Language → Input Sources
5. Assign a third-level chooser and compose key using GNOME Tweaks or layout file includes (if not already defined in the layout file). **Note**: If you have already used `include` statements such as `include "level3(rctrl_switch)"` or `include "compose(menu)"` in your layout file (see §5, below), this step may be redundant. However, in environments where GNOME Tweaks is available, it can serve as a way to verify or temporarily adjust these settings.

## 3. Creating a Custom Keyboard Layout

Layouts are defined in `/usr/share/X11/xkb/symbols/`. Each file contains one or more named layout variants:

```xkb
partial alphanumeric_keys
xkb_symbols "basic" {
    key <AC01> { [ a, A, aelig, AElig ] };
    include "level3(rctrl_switch)" // Enables <right control> as third-level chooser (other options are possible). Must be placed within the `xkb_symbols` block. Avoid duplicating this line elsewhere in the file, as multiple includes of the same feature can trigger load errors.
};
```

* The first entry is the key's normal value
* The second is its value with Shift
* The third and fourth are accessed using a third-level chooser

Avoid using tabs between key values. Use commas with no trailing entries. Save the file using a short identifier (e.g., `oe`) in the `symbols/` directory.

## 4. Registering the Layout in `evdev.xml`

To make the layout visible in your input method selector:

Edit `/usr/share/X11/xkb/rules/evdev.xml` and add information about the layout file you saved in `symbols/` above. The following example is for my Old English layout; if you have designed or are using a different file, replace this information with your own. Make sure to insert this block within the `<layoutList>` section of the XML file. Placing it elsewhere will prevent the system from recognizing the layout.

```xml
<layout>
  <configItem>
    <name>oe</name>
    <shortDescription>OE</shortDescription>
    <description>Old English</description>
    <languageList>
      <iso639Id>eng</iso639Id>
    </languageList>
  </configItem>
  <variantList/>
</layout>
```

## 5. Making the Third-Level Chooser and Compose Key Persistent

To avoid issues where settings disappear on reboot:

* Add this line **inside** your layout block (i.e., within the `xkb_symbols` braces):

```xkb
include "level3(rctrl_switch)"
```

* This sets Right Control (`RCTL`) as the third-level chooser.
* You can also use `ralt_switch`, `lwin_switch`, or others.

**Important**: If you've already defined this in your layout file, you do not need to reassign a third-level chooser in your desktop environment settings. Duplicating this configuration in both the file and settings can cause load errors.

You can also add a "compose" key—i.e., a key (in my case Right Alt) that allows you to combine strokes (such as `` ` `` and `e` to produce accented characters like `è`).

```xkb
key <RALT> { [ Multi_key ] };
```

Again, if this is already defined in your layout file, you do not need to repeat it using GUI tools or desktop settings.

## 6. Using the Layout

To test your layout:

```bash
setxkbmap -layout oe -variant basic
```

Ensure that the `basic` variant mentioned in the above command is actually defined in your layout file (i.e., within a `xkb_symbols "basic" { ... }` block of your layout file as discussed above). If your layout uses a different variant name or has only one unnamed variant, either modify this command accordingly or omit the `-variant` flag entirely. You can list defined variants by inspecting the file in `/usr/share/X11/xkb/symbols/` or using:

```bash
grep 'xkb_symbols' /usr/share/X11/xkb/symbols/oe
```

You can reset the keymap using:

```bash
udevadm trigger --subsystem-match=input --action=change
```

Note: This command is often sufficient to reload keyboard settings without rebooting, but on some systems you may still need to log out or reboot for changes to fully take effect.

## 7. Troubleshooting

Run `xev` to verify keycodes and modifier state:

```bash
xev | grep -A2 --line-buffered 'KeyPress'
```

Use `xkbcomp` to compile and test your layout:

```bash
xkbcomp -w9 -I/usr/share/X11/xkb /usr/share/X11/xkb/symbols/oe $DISPLAY
```

* Make sure you are editing the correct variant and that `setxkbmap` uses the full variant name
* Check for errors like extra commas or undefined values in the layout file
* Verify the layout name in `evdev.xml` matches the file in `symbols/`

---

See `docs/TROUBLESHOOTING.md` for common issues reported by users, including layout registration failures, compose key misbehaviour, and GNOME integration quirks.

For a detailed historical version of this tutorial and additional examples, consult `docs/ARCHIVE.md`.
