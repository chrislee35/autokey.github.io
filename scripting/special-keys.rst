Special keys and key combinations
=================================

``keyboard.send_keys()`` and phrases in **Keyboard** mode understand special keys
written in angle brackets, such as ``<enter>`` or ``<f5>``. A key combination joins
keys with ``+``.

.. code-block:: python

   keyboard.send_keys("<ctrl>+a")               # select all
   keyboard.send_keys("<ctrl>+<shift>+<left>")   # select a word to the left
   keyboard.send_keys("<alt>+<f4>")

Special keys work only when AutoKey types. In the clipboard and mouse selection
modes the text is pasted, so ``<ctrl>+a`` would be pasted as those characters.

Ordinary characters are written as themselves. Names are lowercase.

Modifier keys
-------------

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Name
     - Key
   * - ``<ctrl>``, ``<alt>``, ``<shift>``, ``<super>``, ``<hyper>``, ``<meta>``
     - The modifier, on either side of the keyboard.
   * - ``<left_ctrl>``, ``<left_alt>``, ``<left_shift>``, ``<left_super>``,
       ``<left_hyper>``, ``<left_meta>``
     - The left-hand modifier.
   * - ``<right_ctrl>``, ``<right_alt>``, ``<right_shift>``, ``<right_super>``,
       ``<right_hyper>``, ``<right_meta>``
     - The right-hand modifier.
   * - ``<alt_gr>``
     - The AltGr key.
   * - ``<capslock>``, ``<numlock>`` (and ``<left_...>``, ``<right_...>`` forms)
     - The lock keys.

Cursor and editing keys
-----------------------

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Names
     - Keys
   * - ``<left>``, ``<right>``, ``<up>``, ``<down>``
     - Arrow keys.
   * - ``<home>``, ``<end>``, ``<page_up>``, ``<page_down>``
     - Navigation keys.
   * - ``<insert>``, ``<delete>``, ``<backspace>``
     - Editing keys.
   * - ``<enter>``, ``<tab>``, ``<escape>``
     - Enter, Tab, Escape.
   * - ``<menu>``, ``<pause>``, ``<print_screen>``, ``<scroll_lock>``
     - Other keys.

Function keys
-------------

``<f1>`` to ``<f35>``.

Keypad keys
-----------

``<np_insert>``, ``<np_delete>``, ``<np_home>``, ``<np_end>``, ``<np_page_up>``,
``<np_page_down>``, ``<np_left>``, ``<np_right>``, ``<np_up>``, ``<np_down>``,
``<np_divide>``, ``<np_multiply>``, ``<np_add>``, ``<np_subtract>`` and ``<np_5>``.

Keys with no name
-----------------

A key that has no name in this list can be sent with its numeric code, written
``<codeNN>``, for example ``<code86>``. Use it for keys that have no name here.

Using the names in Python
-------------------------

The same names exist as members of ``autokey.model.key.Key``. In scripts, the
simplest way is to write the string, as above. The full list is the ``Key``
enumeration in ``lib/autokey/model/key.py``.

Hotkeys and the left and right keys
-----------------------------------

The left and right modifier names apply to hotkeys too. A hotkey set for a left
modifier fires only for the left key. See :doc:`../user/triggers`.
