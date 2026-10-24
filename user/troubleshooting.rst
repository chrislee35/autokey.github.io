Troubleshooting
===============

When something does not work, three things usually tell you why: whether AutoKey
is running and monitoring, what its log says, and what a script's error says. This
page shows how to find each, then lists common problems.

Is it running and switched on?
------------------------------

1. Look for the AutoKey icon in the tray, or start AutoKey from a terminal. If
   you start it a second time, it shows the window of the first one instead.
2. In the **Settings** menu, check that **Enable Monitoring** is ticked. When it
   is off, no abbreviation or hotkey does anything. The "toggle monitoring"
   hotkey from the settings switches it, so you may have pressed it by accident.
3. On Wayland, check you finished the setup in :doc:`../install` and that no
   "AutoKey System Configuration Needed" dialog appeared.

Reading the log
---------------

Start AutoKey from a terminal with verbose logging, repeat the action that failed,
and read what AutoKey reports:

.. code-block:: bash

   autokey-gtk --verbose     # or: autokey-qt --verbose

The same messages are written to ``~/.local/share/autokey/autokey.log``. The
**Show log view** menu entry shows the log inside the main window. If you want the
log on a separate viewer, ``--cutelog`` connects to a running
`cutelog <https://github.com/busimus/cutelog>`__.

More options for finding problems:

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Option
     - Adds
   * - ``-v``, ``--verbose``
     - Debug-level messages.
   * - ``-g``, ``--grabkey``
     - Messages about each hotkey AutoKey grabs on X11.
   * - ``-m``, ``--mouse``
     - Messages about mouse button events.
   * - ``-V``, ``--version``
     - Prints AutoKey's version and exits.

Always include the version when you report a problem.

When a script fails
-------------------

When a script raises an error, AutoKey shows a notice. The **Show recorded errors
from Scripts** entry lists the errors from this session: which script crashed,
when, what triggered it and the Python traceback. The last lines of a traceback
usually name the problem, for example a misspelled name or a missing file.

To follow what a script is doing, write messages to AutoKey's log from the script:

.. code-block:: python

   import logging
   from autokey.common import APP_NAME
   script_logger = __import__("autokey.logger").logger.get_logger(APP_NAME + '.script')

   script_logger.debug(f"The script can see these names: {dir()}")

Debug-level messages appear when AutoKey runs with ``--verbose``.

Common problems
---------------

An abbreviation does nothing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Check monitoring is on (above).
- Make sure you typed the ending character. By default an abbreviation expands only
  after a character such as a space, unless **Trigger immediately** is on.
- It expands only at the start of the text or after whitespace, unless **Trigger
  when typed as part of a word** is on.
- A window filter may exclude the window you are typing in. See
  :doc:`window-filters`.
- A mouse click empties AutoKey's record of what you typed, so click first and
  type the whole abbreviation afterwards.
- Abbreviations are case sensitive unless you turned on **Accept any typed
  abbreviation case**.
- Watch the verbose log while you type. It shows what AutoKey sees.

A hotkey does nothing
~~~~~~~~~~~~~~~~~~~~~

- Another program or your desktop may own the combination. Try a different one.
- Check the window filter.
- On X11, run with ``--grabkey`` to see whether AutoKey could grab the key.
- Items can share a hotkey only with different window filters; AutoKey refuses to
  save a clash.

Wrong characters, or case reversed
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you remapped Caps Lock to another key, turn on **Disable handling of the Capslock
key** in the settings and restart AutoKey. If you use several keyboard layouts on
KDE, see :doc:`limitations`.

Text is pasted from the wrong place, or the clipboard is empty
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

AutoKey needs ``xclip`` or ``wl-clipboard`` for the clipboard modes. Install one
(see :doc:`../install`). If a program does not accept pasted text, change the
phrase's **Paste using** setting to **Keyboard**. A terminal usually needs
**Clipboard (Ctrl+Shift+V)**.

Special keys such as ``<left>`` show up as text
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

They only work in **Keyboard** mode. In the clipboard modes they are pasted as the
characters you typed.

The main window does not appear
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run ``autokey-gtk`` or ``autokey-qt`` again, which shows the window of the running
instance, or press the "show configuration window" hotkey. To show it from a
script or a command line, see :doc:`faq`.

AutoKey shows an error at start-up on Wayland
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

"AutoKey System Configuration Needed" means your user cannot yet use ``/dev/uinput``,
is not in the ``input`` group or, on GNOME, lacks the AutoKey extension. Follow the
Wayland setup in :doc:`../install`, then log out and in. "Unsupported Desktop
Environment" means your Wayland desktop is not GNOME or KDE Plasma.

Asking for help
---------------

If you cannot solve it, ask in one of the community channels listed in the README,
or report a bug on the
`issue tracker <https://github.com/autokey/autokey/issues>`__. Include:

1. Your AutoKey version (``autokey-gtk --version``), distribution, desktop and
   whether the session is X11 or Wayland.
2. What you did, what you expected and what happened.
3. The verbose log or the script's traceback, as text and not a screenshot.
