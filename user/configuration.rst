Configuration and backup
========================

AutoKey keeps your phrases, scripts and settings in ordinary files. This page says
where they are, how to back them up and move them to another computer, and what
the main settings do.

Where your data is
------------------

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Path
     - What it holds
   * - ``~/.config/autokey/``
     - Everything AutoKey owns. (If ``XDG_CONFIG_HOME`` is set, the folder is
       ``autokey`` inside it.)
   * - ``~/.config/autokey/autokey.json``
     - Global settings: the settings window's options, the two global hotkeys,
       the paths of any folders you keep outside ``data/``, and the values scripts
       keep in the persistent ``store``. AutoKey also keeps the previous copy next
       to it, as ``autokey.json~``.
   * - ``~/.config/autokey/data/``
     - The default location for folders of phrases and scripts. The first run
       creates **My Phrases** and **Sample Scripts** here.
   * - ``~/.local/share/autokey/autokey.log``
     - The log file. (If ``XDG_DATA_HOME`` is set, the folder is ``autokey`` inside
       it.)

You are not limited to the default location. A top-level folder can be created
anywhere on disk, including inside a synchronised directory, and AutoKey will use it
from there.

Each phrase and script is a pair of files, a content file and a settings file with
the same name and a ``.json`` extension, as described in :doc:`../intro`. Keep each
pair together.

.. note::

   Before AutoKey 0.96.0 the settings files were hidden, with a name that began
   with a dot (``.my_phrase.json``). AutoKey renames them when it upgrades your
   configuration. If you restore an old backup by copying files by hand, remember
   that the hidden files are easy to miss: many copy commands and some file
   managers skip them unless told otherwise.

Back up and restore
-------------------

AutoKey has no separate export feature. Your configuration directory is the backup:

.. code-block:: bash

   cp -r ~/.config/autokey ~/.config/autokey-backup

To restore it, copy it back the same way. Do it while AutoKey is not running if you
can. If you keep phrases and scripts in folders outside ``~/.config/autokey``,
back those up too. ``autokey.json`` records the full path of each such folder, so
on another computer put them at the same path, or add them again from the
**File** menu.

If you restored a backup and your phrases came back without their abbreviations
or hotkeys, the settings files were not copied. Check that each ``.txt`` and
``.py`` file has a ``.json`` file beside it.

Moving to a new computer
------------------------

1. Install the same or a newer AutoKey on the new computer (see
   :doc:`../install`) and run it once, so it creates its folders.
2. Quit AutoKey.
3. Copy your old ``~/.config/autokey`` (and any folders you keep elsewhere) over
   the new one.
4. Start AutoKey again.

AutoKey upgrades the configuration automatically when a newer version reads an
older one, so keep the backup until you are sure everything works.

Editing files outside AutoKey
-----------------------------

AutoKey watches its configuration folders and reloads files that change, so a
phrase you add or edit with a text editor, or a script synchronised from another
computer, is normally picked up while AutoKey is running. If a change does not
appear, restart AutoKey.

To start again from a clean state, quit AutoKey and rename ``~/.config/autokey``.

Settings
--------

Open the settings from the main window (Settings, Configure AutoKey). The most
useful options are:

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Setting
     - What it does
   * - Automatically start AutoKey at login
     - Starts AutoKey with your desktop session.
   * - Show a notification icon
     - Shows or hides the tray icon. If you hide it, use the "show configuration
       window" hotkey to open the main window.
   * - Show configuration window using a hotkey
     - A global hotkey that opens the main window (``<super>+k`` by default).
   * - Toggle monitoring using a hotkey
     - A global hotkey that pauses and resumes all expansions.
   * - Enable undo by pressing backspace
     - Lets Backspace undo the last expansion (see :doc:`phrases`).
   * - Automatically save changes without confirmation
     - Saves edits when you select another item, without asking.
   * - Sort menu items with most frequently used first
     - Orders the popup menus by use.
   * - Trigger menu item by first initial
     - Lets you press the first letter of an entry to choose it in a popup menu.
   * - Allow keyboard navigation of popup menu
     - Lets you move through popup menus with the keyboard.
   * - Disable handling of the Capslock key
     - AutoKey still sees a remapped Caps Lock key as Caps Lock, even though other
       programs see the new key. Turn this on if you remapped Caps Lock and your
       abbreviations do not work, or the case of the output is inverted. Restart
       AutoKey after changing it.
   * - User Module Folder
     - Python files you put in this folder can be imported by your scripts.
   * - UInput Devices (Wayland)
     - The keyboards and mice AutoKey reads on Wayland. Devices that are plugged in
       later are picked up automatically.

The settings window also has options for the tray icon's style and for the colour
theme of the script editor. Some of them take effect after a restart; the window
says which.

Saved values for scripts
------------------------

Scripts can keep values between runs with the persistent ``store``. These values
are saved in ``autokey.json``, so a backup of the configuration directory keeps
them. See :doc:`../scripting/guide`.
