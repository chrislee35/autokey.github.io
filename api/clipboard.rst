Clipboard API
=============

Available in scripts as ``clipboard``. It is the same on every front end and desktop,
and needs ``xclip``, ``xsel`` or ``wl-clipboard`` (``wl-copy`` and ``wl-paste``)
installed. See :doc:`../install`.

Reading the clipboard or the selection when it is empty logs a message and returns
``None``. It does not raise an exception.

.. autoclass:: autokey.scripting.clipboard_pyperclip.PyperclipClipboard
   :members:
   :exclude-members: do_set
