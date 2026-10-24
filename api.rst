API reference
=============

This is the reference for the objects scripts can use. For a tour with examples, see
the :doc:`scripting guide <scripting/guide>`.

Every object is already available in a script; you do not need ``import``
statements. The class paths shown on these pages are not needed in scripts.

.. code-block:: python

   keyboard.send_keys("example document")
   clipboard.fill_clipboard("update clipboard")
   dialog.info_dialog("Info dialog", "Test info dialog")

``dialog`` is the Qt or the GTK implementation, chosen by the front end you are
running. ``window`` is chosen by the kind of session (X11, GNOME Wayland or KDE
Wayland). ``clipboard`` is the same everywhere. ``highlevel`` works on X11 only.

.. toctree::
   :maxdepth: 1

   api/keyboard
   api/mouse
   api/window
   api/clipboard
   api/gtkdialog
   api/qtdialog
   api/system
   api/store
   api/engine
   api/highlevel
   api/common
