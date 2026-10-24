HighLevel API
=============

Available in scripts as ``highlevel``. These functions find images on screen and
click on them. They need an **X11** session. On Wayland ``highlevel`` raises an error
that says so, and a replacement is planned for a later release.

.. automodule:: autokey.scripting.highlevel
   :no-members:
.. autoclass:: PatternNotFound
   :members:
.. autofunction:: visgrep
.. autofunction:: get_png_dim
.. autofunction:: mouse_move
.. autofunction:: mouse_rmove
.. autofunction:: mouse_click
.. autofunction:: mouse_pos
.. autofunction:: click_on_pat
.. autofunction:: move_to_pat
.. autofunction:: acknowledge_gnome_notification
