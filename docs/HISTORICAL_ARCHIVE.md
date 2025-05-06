## HISTORICAL\_ARCHIVE.md — Archive and Commentary

This section documents the evolution of the custom keyboard guide, including contributions, corrections, and commentary from users over the years. It provides historical context for changes and includes selected user feedback.

### A. Origins of the Guide

This guide was first written in December 2006 by Daniel Paul O'Donnell as a response to the lack of clear documentation on defining custom keyboard layouts in X11/Linux systems (you can find the original document [here](https://people.uleth.ca/~daniel.odonnell/blog/custom-keyboard-in-linuxx11)). It was initially tested on Ubuntu 6.10 and 7.04, where system files were located in `/etc/X11/xkb/`.

Over time, changes to Ubuntu and GNOME (notably the transition from `xorg.xml` to `evdev.xml`) required several updates. The guide has since been updated periodically, most recently in 2025, and now assumes file locations consistent with Ubuntu 16.04 and later (`/usr/share/X11/xkb/`).

### B. Key Historical Changes

* **Ubuntu 8.10 Transition**: The `xorg.xml` file was deprecated in favour of `evdev.xml`, requiring changes in where and how custom layouts were registered.
* **GNOME 3+ Modifiers**: Changes to GNOME settings removed GUI support for persistent third-level chooser and compose key assignment, leading to the inclusion of explicit mappings in layout files.
* **Persistent Layout Loading**: Introduced `setxkbmap` and `xkbcomp` as tools for testing and applying layouts without requiring full reboots.
* **Community Contributions**: Numerous users provided comments, especially around bugs, use on other distributions (e.g., Fedora, Debian), and advanced layout construction (e.g., 6-level mappings, IPA support).

### C. Selected Commentary and Contributions

* **[Jedd Schrock](https://people.uleth.ca/~daniel.odonnell/blog/custom-keyboard-in-linuxx11#comment_20200728-1913)** (2020): Demonstrated that Unicode values work more reliably than character names for custom symbols, provided example syntax, and recommended avoiding tabs or trailing commas in layout definitions.
* **[Raek](https://people.uleth.ca/~daniel.odonnell/blog/custom-keyboard-in-linuxx11#comment_20090312-1528)** (2009): Clarified use of `evdev.xml` and the need for `<languageList>` in layout definitions. Offered practical working examples.
* **[Myke](https://people.uleth.ca/~daniel.odonnell/blog/custom-keyboard-in-linuxx11#comment_20210304-1320)** (2021): Suggested placing `include "level3(ralt_switch)"` directly inside the layout block, improving modifier persistence.
* **[CT](https://people.uleth.ca/~daniel.odonnell/blog/custom-keyboard-in-linuxx11#comment_20210718-0355)** (2021): Noted use cases involving multiple scripts and language families and confirmed success on mixed-script keyboards.

### D. Lessons Learned

* Custom layouts are fragile: whitespace, commas, and naming mismatches can silently break functionality.
* GNOME and other DEs often override or ignore system-level definitions.
* Persistent configuration requires both system file editing and runtime validation using tools like `xev` and `xkbcomp`.

### E. Legacy Versions

Earlier versions of the guide are preserved in the `archive/` directory of the GitHub repository. These include:

* `GUIDE-V2006.TXT`: Original 2006 version
* `GUIDE-V2009-EVDEV.XML.TXT`: Adds Ubuntu 8.10+ support
* `GUIDE-V2021-MODIFIER-NOTES.MD`: Covers Gnome Tweaks and modifier overrides
* `COMMENTS-THREAD-ARCHIVED.MD`: Curated user commentary from 2006–2024

These versions provide useful reference for debugging older systems or understanding changes in layout file structure over time.
