Installing AutoKey
==================

.. note::

   This page describes AutoKey 0.97.0 (``develop``). The Wayland steps do not
   apply to earlier releases, which support X11 only.

AutoKey runs on Linux, in an X11 session or in a Wayland session on GNOME or
KDE Plasma. This page helps you pick a front end, install AutoKey, finish the
one-time setup a Wayland session needs, and check that it works.

.. contents::
   :local:
   :depth: 2

Before you start
----------------

**Which front end?** AutoKey comes in two graphical front ends that do the same
job. ``autokey-gtk`` suits GTK desktops such as GNOME, MATE, Cinnamon and Unity.
``autokey-qt`` suits Qt desktops such as KDE Plasma. Either one runs on either
kind of desktop if its libraries are installed. You can install both.

**Which session?** Check which one you are running:

.. code-block:: bash

   echo $XDG_SESSION_TYPE

The answer is ``x11`` or ``wayland``.

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Session
     - Support in 0.97.0
   * - X11 (any desktop)
     - Supported. No extra setup beyond installing the packages.
   * - Wayland on GNOME
     - Supported. Needs access to ``/dev/uinput``, membership of the ``input``
       group, and the AutoKey GNOME Shell extension. See
       :ref:`wayland-setup`.
   * - Wayland on KDE Plasma
     - Supported. Needs access to ``/dev/uinput`` and membership of the
       ``input`` group. No extension is needed. See :ref:`wayland-setup`.
   * - Wayland on any other desktop
     - Not supported. AutoKey shows an "Unsupported Desktop Environment"
       message and stops. Use an X11 session on that desktop instead.

**Requirements.** Python 3.10 or later. For clipboard features, ``xclip`` or
``wl-clipboard`` (see :ref:`clipboard-tools`).

**Back up first if you are upgrading.** AutoKey keeps your phrases, scripts and
settings in ``~/.config/autokey``. Copy that directory before you install a new
version, and remove any old installation first:

.. code-block:: bash

   cp -r ~/.config/autokey ~/.config/autokey-backup

Choose an installation method
-----------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 45 30

   * - Method
     - Best for
     - Does the Wayland setup for you?
   * - :ref:`install-deb`
     - Debian, Ubuntu, Linux Mint and derivatives
     - Mostly (see below)
   * - :ref:`install-pip`
     - Other distributions, or trying a newer version
     - No, follow :ref:`wayland-setup`
   * - :ref:`install-source`
     - Developers and testers
     - No, follow :ref:`wayland-setup`

Distribution packages, where your distribution ships AutoKey, are the easiest
choice, but they can lag behind. The version on the AutoKey wiki's
"Current Linux distributions shipping AutoKey" page shows what each
distribution has.

.. _install-deb:

Debian, Ubuntu and Linux Mint (.deb packages)
---------------------------------------------

1. Download the packages for your release from the
   `releases page <https://github.com/autokey/autokey/releases>`__: the
   ``autokey-common`` package and the ``autokey-gtk`` and/or ``autokey-qt``
   package.
2. Install them together, from the directory where you saved them, so the
   package manager can pull in the dependencies:

   .. code-block:: bash

      sudo apt-get install ./autokey-common_*.deb ./autokey-gtk_*.deb

   Use ``./autokey-qt_*.deb`` as well, or instead, for the Qt front end. The Qt
   front end also needs the ``kdialog`` program. KDE Plasma includes it; on any
   other desktop, install it too (``sudo apt-get install kdialog``), or AutoKey
   shows an error about the missing program when it starts.

The packages are built and installed in an automated test on Ubuntu 24.04 and
26.04. They install ``xclip`` and the other dependencies for you.

**What the installer does for Wayland.** When you install with ``sudo``, the
``autokey-common`` package:

- copies a udev rule to ``/etc/udev/rules.d/10-autokey.rules`` so your session
  can use ``/dev/uinput``;
- adds the user who ran ``sudo`` to the ``input`` group;
- installs the GNOME Shell extension, if GNOME is present. The extension stays
  switched off until you log in again; AutoKey switches it on the first time it
  starts.

If you installed some other way (for example as root without ``sudo``), the
installer prints the two commands you need instead. In every case you must
**log out and log back in** before AutoKey can use the new group membership.
Then continue with :ref:`first-run`.

.. _install-pip:

pip
---

Install the system libraries first. On Debian and Ubuntu:

.. code-block:: bash

   sudo apt-get install python3-dbus python3-pydbus python3-gi python3-evdev \
       python3-pyudev python3-xlib python3-pyinotify libmagic1 xclip wmctrl \
       xautomation imagemagick

For the GTK front end add ``gir1.2-gtk-3.0 gir1.2-gtksource-3.0
gir1.2-notify-0.7 gir1.2-ayatanaappindicator3-0.1 zenity``. For the Qt front
end add ``python3-pyqt5 python3-pyqt5.qsci python3-pyqt5.qtsvg kdialog``. On other
distributions install the packages that provide the same libraries; the full
list is in ``pip-requirements.txt`` in the source tree.

Modern distributions stop ``pip`` from installing into the system Python, and
AutoKey needs the system's GTK, Qt and D-Bus bindings, so use a virtual
environment that can see the system packages:

.. code-block:: bash

   python3 -m venv --system-site-packages ~/autokey-venv
   ~/autokey-venv/bin/pip install "autokey[GTK]"

Use ``"autokey[QT]"`` for the Qt front end, or ``"autokey[GTK,QT]"`` for both.
Start it with ``~/autokey-venv/bin/autokey-gtk`` or
``~/autokey-venv/bin/autokey-qt``.

``pip`` does not do the Wayland setup. If you use Wayland, follow
:ref:`wayland-setup` next.

.. _install-source:

From source
-----------

For developers and testers, install from a clone of the repository. Use the
``develop`` branch for the newest code.

.. code-block:: bash

   git clone https://github.com/autokey/autokey
   cd autokey
   git checkout develop
   python3 -m venv --system-site-packages ~/autokey-venv
   ~/autokey-venv/bin/pip install ".[GTK]"

Use ``".[QT]"`` for the Qt front end. On GNOME, installing from source builds the Shell extension's zip file if
``make`` and ``zip`` are available. The Debian packages can also be built from
a clone with ``debian/build.sh``. The `Contributing and testing guides
<https://github.com/autokey/autokey/blob/develop/CONTRIBUTORS.rst>`__ cover
the development setup in more detail.

.. _wayland-setup:

Wayland setup
-------------

Do this once, on GNOME or KDE Plasma, if you did not install the ``.deb``
packages with ``sudo`` or if the installer told you to. X11 users can skip this
section.

AutoKey reads your keyboard and mouse directly and types through a virtual
keyboard it creates with ``uinput``. That needs three things.

1. **Permission to use /dev/uinput.** Create the udev rule that AutoKey ships:

   .. code-block:: bash

      echo 'KERNEL=="uinput", SUBSYSTEM=="misc", OPTIONS+="static_node=uinput", TAG+="uaccess", GROUP="input", MODE="0660"' \
          | sudo tee /etc/udev/rules.d/10-autokey.rules
      sudo udevadm control --reload
      sudo udevadm trigger --sysname-match=uinput

2. **Membership of the input group.**

   .. code-block:: bash

      sudo usermod -a -G input "$USER"

3. **On GNOME only, the AutoKey Shell extension.** AutoKey uses it to learn
   about windows. If you installed with ``pip``, the extension's zip file is
   under ``share/autokey/gnome-shell-extension/`` inside the environment you
   installed into. From a source clone you can build it with
   ``make -C autokey-gnome-extension``. Install and enable it:

   .. code-block:: bash

      gnome-extensions install --force autokey-gnome-extension@autokey.shell-extension.zip
      gnome-extensions enable autokey-gnome-extension@autokey

   The extension supports GNOME Shell 40 and later. KDE Plasma needs no
   extension: AutoKey talks to KWin directly.

Then **log out and log back in** (or reboot). New group membership and new
GNOME extensions are only picked up at login.

If the setup is incomplete when you start AutoKey, it shows a dialog titled
"AutoKey System Configuration Needed" with the commands for your desktop. If
you run an unsupported Wayland desktop it shows "Unsupported Desktop
Environment" instead.

.. _clipboard-tools:

Clipboard tools
---------------

AutoKey pastes phrases through the clipboard by default, and its scripting
clipboard API uses the same machinery. For that it needs one of these programs:

- ``xclip``, which works on X11 and also on GNOME and KDE Wayland (through
  XWayland); or
- ``wl-clipboard`` (``wl-copy`` and ``wl-paste``), on Wayland.

You need only one. The ``.deb`` packages install ``xclip``. If AutoKey finds
neither on Wayland it logs a warning at startup, and clipboard actions fail
until you install one.

.. _first-run:

First run, and checking that it works
-------------------------------------

1. Start AutoKey from your application menu, or from a terminal with
   ``autokey-gtk`` or ``autokey-qt``. On Wayland, check the dialog described
   above did not appear.
2. A tray icon appears. Click it to open the main window. If you turned the
   icon off, the default shortcut ``<super>+k`` opens the window.
3. Create a test phrase. Choose **My Phrases**, add a phrase with the text
   ``it works``, and give it the abbreviation ``tst``.
4. Open a text editor, type ``tst`` followed by a space, and the text expands.

If nothing happens, run AutoKey from a terminal with verbose logging and read
the log:

.. code-block:: bash

   autokey-gtk --verbose
   less ~/.local/share/autokey/autokey.log

On Wayland, the first lines of the log say what the setup check found, for
example that the user is in the ``input`` group and that ``/dev/uinput`` is
writable.

Troubleshooting installation
----------------------------

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - What you see
     - What to do
   * - "AutoKey System Configuration Needed" dialog
     - Follow :ref:`wayland-setup`, then log out and in. Check
       ``id -nG`` lists ``input`` and ``test -w /dev/uinput && echo ok``
       prints ``ok``.
   * - "Unsupported Desktop Environment" dialog
     - Your Wayland desktop is not GNOME or KDE Plasma. Log in to an X11
       session instead.
   * - Clipboard phrases do nothing, and the log warns about ``xclip``
     - Install ``xclip`` or ``wl-clipboard``.
   * - On GNOME, window features fail
     - Run ``gnome-extensions info autokey-gnome-extension@autokey`` and check
       it says ``Enabled: Yes``. Log out and in after installing it.
   * - ``pip`` refuses with "externally-managed-environment"
     - Use the virtual environment shown in :ref:`install-pip`.
   * - ``dpkg`` reports missing dependencies
     - Install the packages with ``apt-get install ./file.deb`` as shown above
       instead of ``dpkg -i``.

For other problems see the wiki's Troubleshooting page, and report bugs on the
`issue tracker <https://github.com/autokey/autokey/issues>`__ with the
verbose log attached.

Upgrading and uninstalling
--------------------------

AutoKey upgrades your configuration automatically the first time a newer
version starts, which is why the backup above is worth making. To uninstall a
``.deb`` installation:

.. code-block:: bash

   sudo apt-get remove autokey-common autokey-gtk autokey-qt

Removing the packages also removes the udev rule. Your configuration in
``~/.config/autokey`` is left in place.
