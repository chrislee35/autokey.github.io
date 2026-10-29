First steps
===========

This page walks you through the main window and the four things most people do
first: make a folder, add a phrase with an abbreviation, give something a hotkey,
and write a small script. It assumes AutoKey is installed and running (see
:doc:`../install`). Menu and button names are those of the GTK and Qt front ends;
a few differ slightly between them.

The main window
---------------

Open the main window from the tray icon. In the Qt front end, click the icon. In the
GTK front end, a click on the icon (left or right) opens a menu, and **Show Main
Window** is one of its entries. If you hid the icon, press the
global hotkey (``<super>+k`` unless you changed it), or run ``autokey-gtk`` or
``autokey-qt`` again, which asks the running instance to show its window.

The window has a tree of folders on the left. Select an item to see its content
and settings on the right. The menus and the toolbar create, copy, cut, paste,
rename and delete items, record scripts, and open settings and help. The
**Show log view** menu entry shows what AutoKey is doing.

AutoKey creates two folders the first time it runs: **My Phrases**, with a few
sample phrases, and **Sample Scripts**.

Make a folder
-------------

Folders keep related items together and can share a window filter and a trigger.

1. In the **File** menu (or the **New** button on the toolbar) choose **Folder**
   to make a top-level folder, or **Sub-folder** to add one inside the selected
   folder.
2. For a top-level folder you choose where it lives on disk. Accept the default
   to create it under AutoKey's own data folder, or pick any folder, including one
   in a synchronised directory.
3. Type a name and confirm.

Add a phrase with an abbreviation
---------------------------------

1. Select the folder that should hold the phrase.
2. Press ``Ctrl+N``, or choose **Phrase** from the **File** menu. Type a name and
   confirm.
3. Press ``Tab`` or click in the large text box and type the text AutoKey should
   insert.
4. Click **Set** next to **Abbreviation**. In the **Set Abbreviation** dialog,
   enter the abbreviation and choose the options below.
5. Click **OK**, then **Save** (``Ctrl+S``).

Now type the abbreviation, followed by a space or other non-word character, in any
window. AutoKey replaces it with the phrase.

The abbreviation dialog has these options:

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Option
     - What it does
   * - Remove typed abbreviation
     - Erases the abbreviation before inserting the phrase. Leave it on unless you
       want the abbreviation to stay in the text.
   * - Omit trigger character
     - Does not insert the character you typed to finish the abbreviation
       (usually a space).
   * - Match output case to typed abbreviation case (Qt: Match phrase case to typed abbreviation)
     - Adjusts the capitalisation of the phrase to the way you typed the
       abbreviation. With the abbreviation ``tp`` and the phrase "the patient":
       ``tp`` gives "the patient", ``Tp`` gives "The patient" and ``TP`` gives
       "THE PATIENT".
   * - Accept any typed abbreviation case (Qt: Ignore case of typed abbreviation)
     - Lets ``TP`` or ``Tp`` trigger an abbreviation set as ``tp``.
   * - Trigger when typed as part of a word
     - Lets the abbreviation trigger in the middle of a longer word.
   * - Trigger immediately (don't require a trigger character)
     - Expands as soon as the last character of the abbreviation is typed, without
       waiting for a space or other character.
   * - Trigger on
     - Which characters finish an abbreviation: **All non-word** (anything that is
       not a letter, digit or underscore), **Space and Enter**, or **Tab**.

See :doc:`triggers` for the details, including how abbreviations are matched.

Give an item a hotkey
---------------------

Phrases, scripts and folders can all have a hotkey.

1. Select the item.
2. Click **Set** next to **Hotkey**.
3. Click **Press to Set**, then press the key combination you want.
4. Confirm, then save.

A hotkey on a folder opens a menu of the folder's items instead of running one.
You can ask for a specific side of a modifier, for example the left Control key
only; see :doc:`triggers`.

If a combination does not seem to register, check that no other program or your
desktop already uses it. AutoKey cannot take a key combination that the desktop
keeps for itself.

Choose how a phrase is inserted
-------------------------------

A phrase can reach the window in one of five ways. Choose one under **Paste
using** in the phrase's settings:

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Setting
     - What AutoKey does
   * - Keyboard
     - Types the text as key presses. Special keys such as ``<left>`` work in this
       mode only.
   * - Clipboard (Ctrl+V)
     - Puts the text on the clipboard and presses Ctrl+V. This is the default for
       new phrases.
   * - Clipboard (Ctrl+Shift+V)
     - The same, with Ctrl+Shift+V, which terminals usually need.
   * - Clipboard (Shift+Insert)
     - The same, with Shift+Insert.
   * - Mouse Selection
     - Puts the text in the primary selection and presses the middle mouse button.

The clipboard modes restore your previous clipboard afterwards. Clipboard modes
are faster for long text, and keyboard mode is more reliable in programs that do
not accept pasted text.

Other phrase options include **Always prompt before pasting this phrase**, which
asks you to confirm each time, and **Show in notification icon menu**, which adds
the phrase to the tray icon's menu.

Write a script
--------------

1. Select a folder and choose **Script** from the **File** menu. Type a name.
2. Type a few lines of Python in the editor, for example:

   .. code-block:: python

      keyboard.send_keys("Hello from AutoKey")

3. Save, then set a hotkey or abbreviation as you did for a phrase.
4. To try a script without a trigger, press ``F5`` (the **Run** button). The script
   starts after a delay of two seconds, so you can switch to the window you want
   it to act on.

Scripts have ready-made objects such as ``keyboard``, ``mouse``, ``window``,
``clipboard`` and ``dialog``; see :doc:`../scripting/guide`. The **Sample Scripts**
folder has examples to read and copy.

If a script fails, AutoKey shows an error. The **Show recorded errors from
Scripts** menu entry lists the recent ones with their Python tracebacks.

Where to go next
----------------

- :doc:`phrases`: macros that insert the date, the clipboard, command output and more.
- :doc:`triggers`: abbreviations and hotkeys in depth.
- :doc:`window-filters`: make a trigger work only in some windows.
- :doc:`configuration`: where your data is stored, and how to back it up.
- :doc:`troubleshooting`: when something does not work.
