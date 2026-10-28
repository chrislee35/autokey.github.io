Scripting guide
===============

A script is a small Python 3 program that AutoKey runs when you press a hotkey, type
an abbreviation, choose it from a menu or press **Run** (``F5``). Scripts can type
and press keys, click and move the mouse, show dialogs, read and write the
clipboard, manage windows, run programs and keep values between runs.

This page is a tour. The :doc:`API reference <../api>` lists every function.

Writing and running a script
----------------------------

1. In the main window, select the folder where you
   want the script to be saved, right-click on it and choose **Script** from the context menu, and type a name.
2. Write the code in the editor and save.
3. Set a hotkey or abbreviation (see :doc:`../user/triggers`), or press ``F5`` to
   run it. ``F5`` starts the script after two seconds, so you can switch to the
   window it should act on.

Each script is a ``.py`` file with a ``.json`` settings file beside it (see
:doc:`../intro`). You can edit the ``.py`` file with any editor; AutoKey notices the
change. The **Sample Scripts** folder has examples to read and copy. There are many more
in our `wiki <https://github.com/autokey/autokey/wiki/Scripting>`__.

.. TODO: did I add time correctly?

You do not need ``import`` statements for the ``time`` module or for AutoKey's own objects.
They are already available.

The objects a script can use
----------------------------

.. list-table::
   :header-rows: 1
   :widths: 18 82

   * - Object
     - Use it to
   * - ``keyboard``
     - Send text and key presses, press and release keys, and wait for a key.
   * - ``mouse``
     - Click, move, and scroll the mouse, drag over an area, read the pointer's
       location, and wait for a click.
   * - ``window``
     - Find, activate, close, resize, move, and read information about windows.
   * - ``clipboard``
     - Read and write the clipboard and the mouse selection for text as well as images.
   * - ``dialog``
     - Show messages, input boxes, menus, file choosers, and a calendar.
   * - ``system``
     - Run a shell command and create files.
   * - ``store``
     - Keep values between runs of the same script or share them between scripts.
   * - ``engine``
     - Work with AutoKey itself: create phrases, run other scripts, read script
       arguments and the abbreviation that triggered the script.
   * - ``highlevel``
     - Find and click images on screen (X11 only).

Sending text and keys
---------------------

.. code-block:: python

   keyboard.send_keys("Hello from AutoKey")
   keyboard.send_keys("<ctrl>+a")        # a key combination
   keyboard.send_keys("<left><left>")    # special keys, in angle brackets
   keyboard.send_key("<enter>", repeat=2)

``send_keys`` types by default. Special keys and combinations work only when it
types. To paste instead, pass a send mode; then special keys are pasted as text.
The names of special keys are listed in :doc:`special-keys`.

Asking the user
---------------

Dialogs return a pair: an exit code (0 when the user pressed OK) and the value.

.. code-block:: python

   code, name = dialog.input_dialog("Name", "Who is this for?")
   if code == 0:
       keyboard.send_keys("Dear " + name + ",")

The GTK front end draws dialogs with ``zenity`` and the Qt front end with
``kdialog``, so one of them must be installed. See :doc:`../install`.

The clipboard
-------------

.. TODO: Is this fixed? Prior to 0.97.0, all clipboard calls required a
   time.sleep(0.1) to give them time to complete.

.. code-block:: python

   text = clipboard.get_clipboard()
   if text is None:
       dialog.info_dialog("Clipboard", "The clipboard is empty.")
   else:
       clipboard.fill_clipboard(text.upper())

``get_clipboard()`` and ``get_selection()`` return ``None`` when there is nothing to
read. In AutoKey versions before 0.97.0 they raised an exception instead, so older
scripts may wrap them in ``try:``; that is no longer needed, but they should now
check for ``None``. The clipboard needs ``xclip`` or ``wl-clipboard`` installed.

Windows
-------

.. code-block:: python

   title = window.get_active_title()
   window.activate("Calculator", matchClass=False)

How much of the ``window`` API works depends on the desktop. On X11 it uses
``wmctrl``. On GNOME and KDE Wayland a separate implementation is used, backed by the
GNOME Shell extension or KWin. The :doc:`window API pages <../api/window>` list each
implementation's functions.

Shell commands
--------------

.. code-block:: python

   date = system.exec_command("date +%F")     # output, without the final newline

``exec_command`` raises ``subprocess.CalledProcessError`` if the command fails.
Pass ``getOutput=False`` for a program that keeps running; otherwise AutoKey does not
respond to any trigger until it exits.

Keeping values: the store
-------------------------

.. code-block:: python

   count = store.get_value("count")        # None if it was never set
   store.set_value("count", (count or 0) + 1)

``set_value`` and ``get_value`` are private to the script. ``set_global_value`` and
``get_global_value`` share values between all scripts. Values are saved in
``autokey.json``, so they survive a restart. Values must be simple types that can
be saved, such as strings, numbers, lists and dictionaries.

Scripts that react to how they were triggered
---------------------------------------------

.. code-block:: python

   abbreviation, trigger_character = engine.get_triggered_abbreviation()
   if abbreviation is None:
       pass      # run from a hotkey, the menu, the Run button or autokey-run

``engine.get_script_arguments()`` returns the arguments passed by the script that started this one with ``engine.run_script()``, and
``engine.get_macro_arguments()`` the ones given in a ``<script>`` macro's ``args``.
A script run from a macro can return text with ``engine.set_return_value()``.
See :doc:`../user/phrases` for the limits of scripts called from phrases.

Running scripts from outside AutoKey
------------------------------------

``autokey-run --script NAME`` runs a script by name or full path in the running
AutoKey. ``autokey-shell`` opens an interactive Python session with the scripting
objects loaded, which is a quick way to try an API call. ``autokey-headless`` runs
AutoKey with no window, controlled by the scripting API.

Your own modules
----------------

Python files you put in the **User Module Folder** (a setting) can be imported by
your scripts. Currently, these cannot use AutoKey objects (our API calls) without
additional scripting.

Debugging
---------

Errors appear in a notice and in the list of recorded script errors. You can write
messages to AutoKey's log from a script. See :doc:`../user/troubleshooting`.

Differences by desktop
----------------------

- The ``highlevel`` image-matching functions work on X11 only. On Wayland they raise
  an error that says so.
- The ``window`` functions differ between X11, GNOME and KDE.
- Mouse positions on Wayland are tracked by AutoKey itself; test mouse scripts on
  your own desktop (see :doc:`../user/limitations`).

.. TODO:  What does test mouse scripts on your own desktop mean?
