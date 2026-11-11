How AutoKey works inside
========================

A short tour of the code for people who want to change it.

.. note::

   The maintainers keep the working copy in
   `CONTRIBUTORS.rst <https://github.com/autokey/autokey/blob/develop/CONTRIBUTORS.rst>`__
   in the AutoKey repository.

Program structure
-----------------

- Entry points to AutoKey are the user interfaces, which implement
  ``AutokeyApplication``. ``AutokeyApplication`` starts the AutoKey service and
  the file monitor when it initialises.
- The service starts a new ``IoMediator``, ``ScriptRunner`` and
  ``PhraseRunner``.
- If another AutoKey process is already running, the new one tries to show the
  running one's configuration window and then exits. This works through a lock
  file that contains the original process's PID; AutoKey checks that the
  command line of that PID contains "autokey".
- ``autokey`` and ``autokey-run`` communicate over D-Bus.
  ``AutokeyApplication`` registers itself with D-Bus for this.
- ``interface.py`` handles communication with X, which includes grabbing hotkeys
  and sending keypresses. It also handles much of the clipboard mechanics.
- Creating and modifying phrases and hotkeys is done through the
  ``ConfigManager``.

Two issue threads explain parts of this well. Issue
`#255 <https://github.com/autokey/autokey/issues/255>`__ explains how sending
text works, and
`this comment <https://github.com/autokey/autokey/issues/334#issuecomment-564203873>`__
on issue #334 is a detailed walkthrough of how phrases work.

Autocomplete in the script editors
----------------------------------

Both script editors complete the scripting API as you type. Qt's API
autocomplete is simple to implement, but macro and phrase autocomplete is
relatively complex.

Both implementations read the output of ``extractDoc.py``, which uses Python's
``ast`` module to parse the scripting API and the available macros. Developers
should know where that information comes from:

- Arguments come straight from ``ast``, with ``self`` removed if present.
- The description comes from the **first line of the docstring**, so keep it a
  short, concise description of the method.

For macros (currently GTK autocomplete only), the script uses the ``ID``,
``TITLE`` and ``ARGS`` values to generate the lines. The way the translation
function is used changes the AST, so make any new macro look like the existing
ones.

``extractDoc.py`` lists the scripting modules by hand. If you add a new
scripting module, add it to ``extractDoc.py`` as well, or its functions will not
appear in autocomplete.

Run ``python3 extractDoc.py`` whenever you change the scripting API, and commit
the regenerated files.

Qt autocomplete
^^^^^^^^^^^^^^^

API autocomplete uses ``QSci.QsciAPIs``, which reads the content of
``lib/autokey/qtui/data/api.txt``. The Qt editor is built on QScintilla, which
makes this easy.

At present there is no easy way to add autocomplete to the phrase page, which
uses a ``QTextEdit`` widget. Pull requests are welcome.

GTK autocomplete
^^^^^^^^^^^^^^^^

API autocomplete reads ``lib/autokey/gtkui/data/api.csv``, where column 0 is the
API call and column 1 is the description. GTK needs custom handling to read this
data, which takes an entire class.

The same class also handles macro autocomplete, which makes the logic harder to
follow. Separating the two might be simpler to read. Pull requests are welcome.
