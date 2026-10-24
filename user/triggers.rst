Abbreviations and hotkeys
=========================

Every phrase, script and folder can have a **trigger**: an abbreviation you type,
a hotkey you press, or both. This page explains how AutoKey recognises each one.

Abbreviations
-------------

AutoKey keeps a record of the last **150 characters** you typed and checks it
against your abbreviations after each key press. This has some consequences:

- An abbreviation cannot be longer than 150 characters.
- Pressing Backspace moves back through the record only as far as it goes. If
  the abbreviation has already left the record, it will not expand.
- **A mouse click clears the record**, because a click can move you to another
  field or window. You cannot click while you type an abbreviation. A click is a
  handy way to cancel an abbreviation you started by mistake.

A phrase can have more than one abbreviation.

When does an abbreviation expand?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default an abbreviation expands when you type it and then a character that ends
a word: a space, Enter, a punctuation mark or anything else that is not a letter,
digit or underscore. The options in the **Set Abbreviation** dialog change this:

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Option
     - Effect
   * - Trigger on: All non-word
     - The default. Any character that is not a letter, digit or underscore ends
       the abbreviation.
   * - Trigger on: Space and Enter
     - Only a space or Enter ends it.
   * - Trigger on: Tab
     - Only Tab ends it.
   * - Trigger immediately
     - No ending character is needed. The abbreviation expands the moment it is
       typed, as long as it is the last thing typed.
   * - Trigger when typed as part of a word
     - Without it, an abbreviation only expands at the start of the text or right
       after whitespace. With it, ``tp`` also expands inside "footpath".
   * - Remove typed abbreviation
     - Erases the abbreviation. Turn it off to keep it in the text.
   * - Omit trigger character
     - Does not insert the ending character.

The two case options work together:

.. list-table::
   :header-rows: 1
   :widths: 25 25 25 25

   * - You type
     - Neither option
     - Accept any typed case
     - Accept any typed case and match output case
   * - ``tp``
     - the patient
     - the patient
     - the patient
   * - ``Tp``
     - no expansion
     - the patient
     - The patient
   * - ``TP``
     - no expansion
     - the patient
     - THE PATIENT

(The table is for the abbreviation ``tp`` and the phrase "the patient". The Qt
front end calls the options "Ignore case of typed abbreviation" and "Match phrase
case to typed abbreviation".)

.. warning::

   **Match output case** changes the whole phrase, not just its first letter.
   When you type the abbreviation capitalised, the phrase is capitalised with
   Python's ``capitalize()``, which also turns every other capital letter in it
   into lowercase. When you type the abbreviation in capitals the whole phrase
   becomes uppercase, and when you type it in lowercase the whole phrase becomes
   lowercase. Do not use the option on a phrase that must keep its own
   capitalisation, such as a name or an acronym.

Hotkeys
-------

A hotkey is a key, usually with modifier keys, that runs the item when you press
it. Set one with **Set** next to **Hotkey** and press the combination.

- The modifier keys are Control, Alt, Shift and Super. Hyper and Meta are also
  available.
- You can require a particular side of a modifier, for example the left Control
  key only. The GTK front end's hotkey dialog offers the left and right version of
  each modifier; the Qt front end honors such a setting if the configuration file
  contains it. A hotkey set for a specific side must be pressed with that side.
- A hotkey on a **folder** opens a menu of the folder's contents.
- Two items can share a hotkey only if their window filters differ. See
  :doc:`window-filters`.
- AutoKey cannot use a combination that your desktop or another program has
  already taken for itself.

Special hotkeys in the settings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The AutoKey settings have two hotkeys of their own: one to **show the
configuration window** (``<super>+k`` by default) and one to **toggle monitoring**,
which pauses and resumes all expansions.

Recording a key combination
~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you do not know how a key is named, click **Press to Set** and press it. The
dialog records what AutoKey sees.

Menus and prompts
-----------------

Besides running at once, an item can:

- ask first (**Always prompt before pasting this phrase** or **Always prompt
  before executing this script**);
- appear in the tray icon's menu (**Show in notification icon menu**).

In the settings, the popup menu can be sorted with the most-used items first, and
navigated with the keyboard.
