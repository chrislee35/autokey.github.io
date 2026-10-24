Known limitations
=================

Things AutoKey cannot do, or does differently depending on the desktop. Some have
workarounds. If you find another, please report it.

On every desktop
----------------

Abbreviations
~~~~~~~~~~~~~

- AutoKey remembers only the last **150 characters** you typed. An abbreviation
  cannot be longer than that, and Backspace cannot take you back beyond it.
- **A mouse click empties that record**, so you cannot click while you type an
  abbreviation. Frequent clicks, for example from an auto-fire mouse button,
  interfere with abbreviations.
- A window filter is matched from the start of the window title or class, not
  anywhere inside it. See :doc:`window-filters`.

Phrases
~~~~~~~

- Special keys (``<left>``, ``<ctrl>+a`` and so on) work only in **Keyboard** mode.
  In the clipboard and mouse selection modes they are pasted as text.
- A phrase may contain only one ``<cursor>`` macro.
- A script run through the ``<script>`` macro must not change what is on the screen:
  no typing, no mouse clicks, no desktop-automation tools. See :doc:`phrases`.
- **Undo** (Backspace right after an expansion) does not work when the expanded text
  contains special keys, including ones produced by macros. It can also go wrong when
  a phrase uses the Tab character to move between fields, or when your editor turns
  tabs into spaces.
- **Match output case** rewrites the whole phrase's capitalisation, not only the
  first letter. See :doc:`triggers`.

Desktop features
~~~~~~~~~~~~~~~~

- If you turn on the **Sticky Keys** accessibility option, your desktop processes
  those keys itself and AutoKey may see them differently from what you pressed.
- AutoKey cannot take a hotkey that your desktop or another program has already
  reserved for itself.
- Keyboard layouts: on KDE, using a layout other than the first in your list has been
  reported to produce wrong characters. The workaround is to switch to the first
  layout before using AutoKey. This has not been re-checked on Wayland.

On Wayland (GNOME and KDE Plasma)
---------------------------------

- **Only GNOME and KDE Plasma are supported.** On any other Wayland desktop AutoKey
  stops at start-up with an "Unsupported Desktop Environment" message. Use an X11
  session there.
- **The session needs set-up.** AutoKey reads input devices directly and types with
  a virtual keyboard created through ``/dev/uinput``, so your user needs access to it
  and membership of the ``input`` group, and on GNOME the AutoKey Shell extension.
  See :doc:`../install`.
- **Image matching is not available.** The functions that find an image on screen
  (``visgrep``, ``click_on_pat``, ``move_to_pat``) need an X11 root window. On
  Wayland they raise a clear error. A replacement is planned for a later release.
- **Window information comes from the desktop.** On GNOME it comes from the AutoKey
  Shell extension and on KDE from KWin scripting, so window titles and classes can
  differ from what X11 reports. Use **Detect Window Properties** to see the values
  to put in a filter.
- **The clipboard needs a helper program** (``xclip`` or ``wl-clipboard``).
- **Mouse positions are tracked by AutoKey.** On Wayland AutoKey moves the pointer
  with relative movements and remembers where it thinks the pointer is. If something
  else moves the pointer, absolute mouse positions in scripts can land off target.
  Test mouse scripts on your own desktop.
- **Input from other automation tools may not reach AutoKey.** Programs that inject
  input through the X11 test extension (such as ``xdotool key``) do not pass through
  the keyboard devices AutoKey reads on Wayland.

On X11
------

- The image-matching functions and some window functions rely on helper programs
  (``xautomation``, ``imagemagick``, ``wmctrl``). They are listed in
  :doc:`../install`.

Reporting a limitation
----------------------

If AutoKey behaves differently from what this page says, please report it on the
`issue tracker <https://github.com/autokey/autokey/issues>`__ with the verbose log
(see :doc:`troubleshooting`).
