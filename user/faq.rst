Frequently asked questions
==========================

Can I use AutoKey on Wayland?
-----------------------------

Yes, on GNOME and KDE Plasma, from version 0.97.0. It needs a one-time setup,
described in :doc:`../install`. Other Wayland desktops are not supported; use an
X11 session on them. Some features, such as image matching, are not available on
Wayland (see :doc:`limitations`).

Which front end should I install, GTK or Qt?
--------------------------------------------

Either. ``autokey-gtk`` suits GTK desktops such as GNOME and Cinnamon, and
``autokey-qt`` suits KDE Plasma. They do the same things. See :doc:`../install`.

How can I pause and resume AutoKey?
-----------------------------------

Use the **Enable Monitoring** entry in the Settings menu, or the "toggle
monitoring" hotkey you can set in the settings (**Toggle monitoring using a
hotkey**). From a terminal you can use D-Bus:

.. code-block:: bash

   dbus-send --session --type=method_call --dest=org.autokey.Service \
       /AppService org.autokey.Service.pause_service
   dbus-send --session --type=method_call --dest=org.autokey.Service \
       /AppService org.autokey.Service.unpause_service

How can I show the main window?
-------------------------------

Click the tray icon. If you hid the icon, use the "show configuration window"
hotkey from the settings (``<super>+k`` by default).

To do it from a command line or a script, start AutoKey again (``autokey-gtk`` or
``autokey-qt``): the new instance asks the running one to show its window, then
exits. You can also send the request directly:

.. code-block:: bash

   dbus-send --session --type=method_call --dest=org.autokey.Service \
       /AppService org.autokey.Service.show_configure

Can I run a phrase or script from the command line?
---------------------------------------------------

Yes, with ``autokey-run``, which asks the running AutoKey to do it:

.. code-block:: bash

   autokey-run --script "My script"      # a script, by name or full path
   autokey-run --phrase "My phrase"      # paste a phrase
   autokey-run --folder "My folder"      # pop up a folder's menu

Where is my configuration stored, and can I copy it to another computer?
------------------------------------------------------------------------

In ``~/.config/autokey``. See :doc:`configuration` for what is in it, how to back it
up and how to move it.

How do I back up or restore AutoKey?
------------------------------------

Copy the configuration directory. See :doc:`configuration`.

I remapped Caps Lock and my abbreviations stopped working
---------------------------------------------------------

Turn on **Disable handling of the Capslock key** in the settings and restart
AutoKey. See :doc:`configuration`.

What are the trigger characters?
--------------------------------

The characters that end an abbreviation, so that it expands. By default it is any
character that is not a letter, digit or underscore. You can change it for each
abbreviation to "Space and Enter" or "Tab", or turn on "Trigger immediately". See
:doc:`triggers`.

How do I type a special character, such as one that is not on my keyboard?
---------------------------------------------------------------------------

Put the character itself in the phrase. Phrases are saved as UTF-8 text, so you
can paste in any character, for example from your desktop's character map, or
type it in a GTK text box with ``Ctrl+Shift+U`` followed by its hexadecimal code
(for example ``Ctrl+Shift+U``, ``e9``, ``Enter`` for é). With the default
**Clipboard (Ctrl+V)** setting under **Paste using**, the character arrives
unchanged. If you set the phrase to **Keyboard**, AutoKey has to type each
character, and a character with no key on your layout may not work in every
program.

Phrases have no way to write a character by its Unicode code number. The
``<code86>`` form that you may see in AutoKey's text is not Unicode: the number is
the keyboard's hardware key code, and it is used for keys AutoKey has no name for.

In a script, Python can write a character by its code. This script puts é on the
clipboard and pastes it:

.. code-block:: python

   clipboard.fill_clipboard("\u00e9")      # or chr(0xE9)
   keyboard.send_keys("<ctrl>+v")

Does AutoKey run scripts written for AutoHotkey on Windows?
-----------------------------------------------------------

No. AutoKey scripts are written in Python, which can do much of what AutoHotkey
scripts do on Windows.

Is AutoKey available for Windows or macOS?
------------------------------------------

No. AutoKey is for Linux.

What is the license?
--------------------

AutoKey is released under the GNU General Public License, version 3.

How do I suggest a feature or report a bug?
-------------------------------------------

Use the `issue tracker <https://github.com/autokey/autokey/issues>`__. For a bug,
include the information listed in :doc:`troubleshooting`. For help using AutoKey,
ask in one of the community channels listed in the README.
