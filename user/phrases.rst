Phrases and macros
==================

A phrase is a piece of text that AutoKey inserts when you type its abbreviation or
press its hotkey. This page covers what can go inside a phrase, how macros make a
phrase dynamic, and how to undo an expansion. To make a phrase, see
:doc:`first-steps`.

What a phrase can contain
-------------------------

A phrase can contain ordinary text, including several lines and non-ASCII
characters. Characters outside the basic Latin set are most reliable in the
clipboard modes. A phrase can also contain **macros** and, in keyboard mode,
**special keys**.

How the text is inserted depends on the phrase's **Paste using** setting (see
:doc:`first-steps`). This matters for what you put in the phrase:

- In **Keyboard** mode AutoKey types the text, and special keys written as
  ``<left>``, ``<ctrl>+a`` and similar are sent as key presses. See
  :doc:`../scripting/special-keys` for the names.
- In the **clipboard** and **mouse selection** modes the text is pasted, so
  special keys cannot work. Writing ``<ctrl>+<f11>`` in such a phrase pastes those
  characters, not the key combination.

Macros
------

A macro is a tag inside a phrase that AutoKey replaces with text, or that makes it
do something, when it expands the phrase. To add one, use the **Insert Macro** menu entry
in the phrase editor, which fills in the tag and its arguments for you.

Arguments are written as ``name="value"`` inside the tag.

.. list-table::
   :header-rows: 1
   :widths: 25 30 45

   * - Macro
     - Arguments
     - What it does
   * - ``<cursor>``
     - none
     - Puts the text cursor at this position after the phrase is inserted. A
       phrase may contain only one.
   * - ``<date format="...">``
     - ``format``
     - Inserts the current date and time. The format is a Python ``strftime``
       format, for example ``<date format="%Y-%m-%d">`` gives 2026-10-23.
   * - ``<clipboard>``
     - none
     - Inserts what is on the clipboard.
   * - ``<file name="...">``
     - ``name``
     - Inserts the contents of a text file. Give the absolute path. Use it for
       small text files: AutoKey reads the whole file and types or pastes all of
       it. A phrase file can be included this way too.
   * - ``<system command="...">``
     - ``command``
     - Runs a command and inserts its output, for example
       ``<system command="hostname">``.
   * - ``<script name="..." args="...">``
     - ``name``, ``args``
     - Runs an AutoKey script and inserts what it prints. ``name`` is the script's
       name, or the absolute path of a script outside AutoKey. ``args`` is a
       comma-separated list of values the script reads with
       ``engine.get_macro_arguments()``. The argument is required, so give a
       dummy value if the script does not use any.

A phrase with macros is called a dynamic phrase.

Limits of the script and system macros
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

AutoKey runs the scripts and commands in a phrase **before** it removes the
typed abbreviation and inserts the text. For that reason a script used through
``<script>`` must not change what is on the screen. In particular it must not:

- type or send keys with ``keyboard``,
- click with ``mouse``, or
- run tools that automate the desktop through ``system.exec_command``.

A script used this way may do background work, such as reading data or starting a
service, and print the text to insert. If you need a script to type or click, run
it directly from its own hotkey or abbreviation instead.

Undoing an expansion
--------------------

If **Enable undo by pressing backspace** is on (a setting in the AutoKey settings),
pressing Backspace right after an expansion removes the expanded text and puts the
abbreviation back.

Undo works only when AutoKey can tell what the expansion typed. It is turned off
for an expansion whose final text contains special keys, including special keys
produced by a macro such as ``<script>`` or ``<file>``. Undo may also go wrong
in a phrase that moves between form fields with the Tab character, and in editors
that turn tab characters into spaces.

Where to go next
----------------

- :doc:`triggers`: how abbreviations and hotkeys are matched.
- :doc:`window-filters`: limit a phrase to some windows.
- :doc:`../scripting/guide`: write the scripts a phrase can call.
