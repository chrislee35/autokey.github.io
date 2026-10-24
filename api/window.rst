Window API
==========

Available in scripts as ``window``. AutoKey picks the implementation when it starts,
from the kind of session it is running in:

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Session
     - Implementation
   * - X11
     - :doc:`window_x11`, which uses ``wmctrl`` and ``xrandr``.
   * - Wayland on GNOME
     - :doc:`window_gnome`, which uses the AutoKey GNOME Shell extension.
   * - Wayland on KDE Plasma
     - :doc:`window_kde`, which uses KWin scripting.

The three provide the same functions, but what each can do depends on what the
desktop allows, so check the page for your desktop.

.. toctree::
   :hidden:

   window_x11
   window_gnome
   window_kde
