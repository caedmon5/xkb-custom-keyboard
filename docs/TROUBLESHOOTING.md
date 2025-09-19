## Troubleshooting

Here are some common problems users encounter when setting up or using a custom keyboard layout, along with recommended solutions.

### 1. My custom layout doesn't appear in the Input Sources GUI

**Cause:** The layout is not registered correctly in `/usr/share/X11/xkb/rules/evdev.xml`.

**Solution:**

* Double-check that your layout block is inside the `<layoutList>` section.
* Ensure the `<name>` matches your file name in `/usr/share/X11/xkb/symbols/`
* Use a valid ISO 639-2 language code in the `<iso639Id>` field.

Example:

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

### 2. `setxkbmap` gives an error or does nothing

**Cause:** The variant name may be missing, incorrect, or not defined in your layout file.

**Solution:**

* Confirm that your layout file (e.g., `oe`) includes a block like:

  ```xkb
  xkb_symbols "basic" {
  ```
* If not, adjust your `setxkbmap` command accordingly:

  ```bash
  setxkbmap -layout oe
  # or, if "basic" is present
  setxkbmap -layout oe -variant basic
  ```
* You can check variants by running:

  ```bash
  grep 'xkb_symbols' /usr/share/X11/xkb/symbols/oe
  ```

### 3. My third-level chooser or compose key doesn't work

**Cause:** These modifiers must either be defined in your layout file *or* assigned through your desktop environment. Definitions from GNOME Tweaks are not always persistent across reboots.

**Solution (as used in `oe`):**

Add explicit mappings to your layout file (e.g. `oe` in `/usr/share/X11/xkb/symbols/`):

```xkb
// Set Right Control as third-level chooser
key <RCTL> { [ ISO_Level3_Shift ] };

// Set Right Alt as Compose (Multi_key)
key <RALT> { [ Multi_key ] };
```

This method avoids the risk of duplicating `include` statements and allows persistent modifier assignment across sessions. Ensure this block is inside your `xkb_symbols "..."` definition.

### 4. dead_macron + æ/Æ yields plain æ + combining macron (or nothing)

**Symptoms:** Your dead-macron works on vowels (ā, ē, …) but not on æ/Æ.

**Cause:** No default Compose rule for `<dead_macron> <ae|AE>`.

**Fix (Compose):**

```text
include "%L"

<dead_macron> <ae> : "ǣ" U01E3
<dead_macron> <AE> : "Ǣ" U01E2
```

If `%L` fails, use:

```text
include "/usr/share/X11/locale/en_US.UTF-8/Compose"
```

Restart apps / relogin.

**Alternative (XKB direct mapping):**

```xkb
key <AD03> { [ e, E, U01E3, U01E2 ] };
```

**Checks:**
- `xev` shows dead_macron then æ/Æ before composition.
- The result is U+01E3 / U+01E2, not æ + U+0304.
- Your font supports ǣ/Ǣ.

### 5. Changes don't take effect

**Cause:** X11 caching or stale configuration.

**Solution:**
Try one of the following:

```bash
udevadm trigger --subsystem-match=input --action=change
```

If that fails, try:

```bash
sudo rm -rf /var/lib/xkb/*
sudo dpkg-reconfigure xkb-data
reboot
```

### 6. Debugging the keyboard mapping

Use `xev` to see how your keys are interpreted:

```bash
xev | grep -A2 --line-buffered 'KeyPress'
```

Use `xkbcomp` to test loading your layout:

```bash
xkbcomp -w9 -I/usr/share/X11/xkb /usr/share/X11/xkb/symbols/oe $DISPLAY
```

If errors occur, inspect the output for:

* Invalid key names
* Empty values (e.g., `[ a, A, , ]`)
* Missing or duplicate `include` lines
