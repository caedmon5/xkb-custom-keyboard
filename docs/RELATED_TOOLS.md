## RELATED\_TOOLS.md — Tools, Editors, and References

This document lists useful software, utilities, and documentation for designing, testing, and maintaining custom XKB keyboard layouts under Linux/X11 systems. It complements the main guide and historical archive.

### A. Configuration and Testing Tools

* **xev**
  A utility to display X11 events, including keypresses. Useful for verifying key mappings and modifiers.

  ```bash
  xev | grep keycode
  ```

* **xmodmap**
  Older tool for modifying keymaps in X11. Can be useful for quick tests but is generally deprecated in favour of XKB.

* **setxkbmap**
  Command-line tool to change keyboard layouts and options.

  ```bash
  setxkbmap -layout us -variant altgr-intl
  ```

* **xkbcomp**
  Compiles an XKB keyboard description into a binary form usable by the X server. Can also extract and debug layouts.

  ```bash
  xkbcomp $DISPLAY layout_dump.xkb
  ```

### B. Layout Browsing and Editing

* **GNOME Tweaks (deprecated for some layout settings)**
  Useful for GUI-level third-level chooser and compose key settings (not persistent in all cases).

* **kbdlayout-viewer** (third-party, may require build)
  GUI tool for exploring and visualising layout variants.

* **Text Editors**
  Recommended: `gedit`, `kate`, `vim`, `nano`
  Avoid: `xed`, as it may insert metadata that corrupts XML files.

### C. Documentation and Guides

* [ArchWiki: Xorg Keyboard Configuration](https://wiki.archlinux.org/title/Xorg/Keyboard_configuration)
  Comprehensive reference for XKB setup and troubleshooting across distros.

* [Ubuntu Keyboard Layouts Thread](https://askubuntu.com/questions/482678/how-to-add-a-new-keyboard-layout-custom-keyboard-layout-definition)
  Example-driven discussion with links to current file locations and naming.

* [xkbcommon documentation](https://xkbcommon.org/doc/current/)
  For understanding the XKB model and integrating with Wayland or advanced apps.

### D. Font and Input System Interoperability

* **Fontconfig**
  Ensure that the glyphs your layout emits are supported by the selected fonts.

* **ibus / fcitx / xim**
  Input method frameworks used on various Linux systems. May override or bypass XKB settings.
  To enforce XKB:

  ```bash
  export GTK_IM_MODULE=xim
  ```
