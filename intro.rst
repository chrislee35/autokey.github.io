Introduction to AutoKey
=======================

.. note::

   This documentation is for AutoKey 0.97.0 (``develop``).

AutoKey is a desktop automation utility for Linux. It watches what you type and
the shortcut keys you press, and when it sees one of your triggers it expands a
piece of text for you or runs a Python script. It works in an X11 session, and in
a Wayland session on GNOME or KDE Plasma.

This page explains the words AutoKey uses and how its pieces fit together. To
install it, see :doc:`install`. To start using it, see :doc:`user/first-steps`.

What AutoKey does for you
-------------------------

AutoKey can save time and reduce typing and strain on your hands.

- There are things you type again and again: your address, your company name, a
  signature. An abbreviation can expand a few characters into any block of text.
- For programmers, text insertion can add code tags, dividers and boilerplate at
  the cursor.
- If you use software that needs long key combinations, AutoKey can send them with
  one keystroke.
- A script can do more than insert text. It can work with several running
  programs, move text between them, start and close them, and send keyboard and
  mouse input to them.
- AutoKey works across applications. You set a phrase, script or abbreviation up
  once, and it works everywhere, unless you limit it to chosen windows.

Glossary
--------

abbreviation
   A short sequence of letters, numbers or symbols that, when typed, triggers
   AutoKey to do something: expand a phrase, run a script or show a menu.

hotkey
   A key or combination of keys that, when pressed, triggers AutoKey to do
   something. A hotkey can be any key on the keyboard, with or without modifier
   keys (Shift, Control, Alt, Super, and others). You can ask for the left or the
   right modifier key specifically.

trigger
   An abbreviation or a hotkey.

phrase
   A piece of text. A phrase can contain macros, which are replaced with
   generated text when the phrase is expanded.

macro
   A tag such as ``<date>`` or ``<cursor>`` inside a phrase. When the phrase is
   expanded the tag is replaced with text, or does something, for example it
   places the text cursor. See :doc:`user/phrases`.

script
   A small Python program. It can send keys and mouse clicks, show dialogs, start
   programs, move and resize windows, read and write the clipboard, and more. See
   :doc:`scripting/guide`.

folder
   A group of phrases, scripts and other folders, shown in the tree on the left of
   the main window. A folder can have a trigger and a window filter of its own.

window filter
   A rule that limits a trigger to the windows you choose, or excludes the windows
   you choose. See :doc:`user/window-filters`.

tray icon
   The AutoKey icon in the notification area. Clicking it opens a menu with
   AutoKey's main actions. You can hide it in the settings.

How things are organized
------------------------

In the main window, folders appear in a tree on the left. Each folder contains
phrases and scripts, and may contain more folders. The settings for the selected
item appear on the right.

On disk, each phrase and script is a pair of files in the same directory:

.. list-table::
   :header-rows: 1
   :widths: 25 35 40

   * - Item
     - Content file
     - Settings file
   * - Phrase
     - ``my_phrase.txt``
     - ``my_phrase.json``
   * - Script
     - ``my_script.py``
     - ``my_script.json``
   * - Folder
     -
     - ``folder.json`` inside the folder

The settings file holds the abbreviation, hotkey, window filter and send mode.
Keep each pair together when you copy or move files. See
:doc:`user/configuration` for where the files are and how to back them up.

.. note::

   Before AutoKey 0.96.0 the settings files were hidden (their names began with a
   dot). AutoKey renames them automatically when it upgrades your configuration.

How AutoKey works
-----------------

AutoKey runs in the background and does three things.

1. **It watches the keyboard and mouse.** On X11 it listens to input events
   through the X server. On Wayland it reads the keyboard and mouse devices
   directly.
2. **It decides what to do.** It keeps a short record of what you typed (the last
   150 characters) to recognise abbreviations, and it checks the keys you press
   against your hotkeys. A window filter can limit where a trigger applies.
3. **It acts.** It expands a phrase, runs a script or shows a menu. To insert text
   it either types the characters or pastes them through the clipboard, as you
   choose for each phrase.

Typing and clipboard behavior differ a little between X11 and Wayland, and some
keyboard layouts have known problems. :doc:`user/limitations` lists them.

Where to go next
----------------

- :doc:`install`: install AutoKey and, on Wayland, finish the setup.
- :doc:`user/first-steps`: add a folder, a phrase with an abbreviation, a hotkey
  and a script.
- :doc:`user/troubleshooting` and :doc:`user/faq`, if something does not work.
- :doc:`scripting/guide` and the :doc:`API reference <api>`, to write scripts.
