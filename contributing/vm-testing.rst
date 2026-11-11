Testing on virtual machines
===========================

AutoKey's automated tests mock the display server, the input devices and the
desktop-specific IPC. That keeps them fast and portable, but they cannot catch
bugs that only appear when AutoKey drives a *real* desktop session: a hotkey that
never reaches a real X11 grab, a clipboard paste that a real GTK or Qt window
handles differently from a mock, or a window-detection call that behaves
differently on Wayland than on X11.

To catch these bugs, the maintainers test AutoKey inside virtual machines that
each run one real desktop environment. This page explains how to build that
fleet, and how to run both the automated and the manual checks against it. For
what to check, see :ref:`manual-checklist`.

.. note::

   The maintainers keep the working copies of this material in the AutoKey
   repository:
   `VM_TESTING.md <https://github.com/autokey/autokey/blob/develop/VM_TESTING.md>`__,
   `tests/vm_checklist/README.md <https://github.com/autokey/autokey/blob/develop/tests/vm_checklist/README.md>`__
   and
   `tests/manual/README.md <https://github.com/autokey/autokey/blob/develop/tests/manual/README.md>`__.

The VM fleet
------------

Three virtual machines cover AutoKey's three supported display environments:

.. list-table::
   :header-rows: 1
   :widths: 20 30 25 25

   * - VM alias
     - Distribution
     - Desktop
     - Session type
   * - m22
     - Linux Mint 22
     - Cinnamon
     - X11
   * - u24
     - Ubuntu 24.04
     - GNOME
     - Wayland
   * - k26
     - Kubuntu 26.04
     - KDE Plasma
     - Wayland

m22 is the only X11 machine in the fleet. Keep it, because some of AutoKey's
scripting API, such as the image-matching functions ``visgrep`` and
``click_on_pat``, works only on a real X11 root window and cannot be tested
meaningfully on Wayland.

Creating a VM
-------------

Each VM starts as a plain VirtualBox install and is brought to a known, reusable
state. Do these steps once per VM, then take a snapshot so you can return to a
clean base before testing a fresh install again.

1. **Install the OS.** Use the standard installer for the distribution and
   desktop in the table above.
2. **Update packages.**

   .. code-block:: bash

      sudo apt update
      sudo apt install -y openssh-server

3. **Set up SSH port forwarding.** In VirtualBox's network settings for the VM,
   add a port-forwarding rule ``127.0.0.1:<host-port> -> 10.0.2.15:22``. Use a
   different ``<host-port>`` for each VM: 2222 for m22, 2223 for u24 and 2224
   for k26.
4. **Add the VM to your SSH config.** In ``~/.ssh/config`` on the host, so you
   can reach it by a short alias:

   .. code-block:: text

      Host m22
          HostName 127.0.0.1
          Port <host-port>
          User autokey
          IdentityFile ~/.ssh/<your-key>

5. **Copy your SSH key to the guest and set permissions,** so later automation
   can log in without a password prompt:

   .. code-block:: bash

      scp ~/.ssh/<your-key>.pub m22:~/key.pub
      ssh m22 "mkdir -p ~/.ssh && cat ~/key.pub >> ~/.ssh/authorized_keys && \
          chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"

6. **Test passwordless SSH** with ``ssh m22 echo ok``. If it asks for a password,
   check step 5 before you go on.
7. **Set dark mode** in the guest's appearance settings. This is cosmetic, but it
   makes screenshots taken during automated checks easier to read and keeps them
   consistent across VMs.
8. **Let the** ``autokey`` **user use sudo without a password:**

   .. code-block:: bash

      echo "autokey ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/autokey
      sudo chmod 440 /etc/sudoers.d/autokey

   .. warning::

      This is a convenience for disposable test VMs only. Never set it up on any
      other machine.

9. **Install a terminal editor** for quick edits on the VM, for example
   ``sudo apt install -y neovim``.
10. **Create a clock-resync script.** A VM's clock drifts while it is stopped, and
    a stale clock makes ``apt-get update`` fail with "Release file is not valid
    yet". Save this as ``~/timesync.sh`` and run ``chmod 700`` on it:

    .. code-block:: bash

       #!/usr/bin/env bash
       # Works for either time-sync service; run whichever is present.
       if systemctl is-enabled chronyd &>/dev/null; then
           sudo systemctl restart chronyd
       else
           sudo timedatectl set-ntp false
           sudo timedatectl set-ntp true
       fi

    Run it after starting a VM that has been stopped for a while, before any
    ``apt`` command.
11. **Upgrade the rest of the system:** ``sudo apt dist-upgrade -y``.
12. **Turn off automatic updates** in the guest's software settings, so that a
    scheduled update cannot change the environment in the middle of a test run.
13. **Reboot** the VM.
14. **Take a VirtualBox snapshot named** ``installed``. It captures a clean base
    OS with nothing AutoKey-related installed, so you can return to it whenever
    you need to repeat fresh-install testing, such as checking
    ``apt-requirements.txt`` or first-run behavior.

Installing AutoKey on a VM
^^^^^^^^^^^^^^^^^^^^^^^^^^

After the ``installed`` snapshot, clone AutoKey and install its dependencies on
each VM:

.. code-block:: bash

   git clone -b develop https://github.com/autokey/autokey.git
   cd autokey
   sudo apt install -y $(cat debian/build_requirements.txt)
   sudo apt install -y $(cat apt-requirements.txt)

On GNOME (u24), also build and install the GNOME Shell extension, then copy over
AutoKey's icons and the udev rule:

.. code-block:: bash

   make -C autokey-gnome-extension
   gnome-extensions install autokey-gnome-extension/autokey-gnome-extension@autokey.shell-extension.zip
   gnome-extensions enable autokey-gnome-extension@autokey
   mkdir -p ~/.local/share/icons
   cp -vr config/*.png config/*.svg config/Humanity config/ubuntu-mono-* ~/.local/share/icons/
   sudo cp config/10-autokey.rules /etc/udev/rules.d/
   sudo reboot

See :ref:`install-source` and :doc:`guide` for the full dependency and build
details. Take a second snapshot once AutoKey runs cleanly, so that day-to-day
testing does not have to repeat the dependency install.

Running the live-VM checklist
-----------------------------

``tests/vm_checklist/checklist.py`` runs on your host and automates most of the
core manual checklist (phrase expansion, hotkey triggering, each paste method,
the enable/disable toggle and config-window sanity) against a live VM. It
connects out over SSH and drives the VM's real desktop session with ``xdotool``
(X11) or ``ydotool`` (Wayland). It then checks AutoKey's behavior against real
evidence: AutoKey's debug log, or the output files written by the paste probes
described below.

Requirements
^^^^^^^^^^^^

On the host:

- ``ssh`` and ``scp``, with a passwordless SSH alias for the VM already set up.
- ``VBoxManage``, if you want screenshots on failure. It is optional; the script
  works without it, for example on a machine that is not a VirtualBox VM.

On the VM:

- ``xdotool``, for window discovery and activation on X11 and Wayland.
- ``ydotool`` and ``ydotoold``, on Wayland sessions only. They do the real input
  injection, because ``xdotool``'s events never reach XWayland clients.
- AutoKey itself, already running in a real desktop session.

Usage
^^^^^

.. code-block:: bash

   python3 tests/vm_checklist/checklist.py --list
   python3 tests/vm_checklist/checklist.py --vm m22
   python3 tests/vm_checklist/checklist.py --vm k26 --items 1,3,4
   python3 tests/vm_checklist/checklist.py --vm u24 --repo-path ~/autokey --keep-phrases

Each run writes a JSON report and, on any failure, a screenshot under
``tests/vm_checklist/checklist-results/``.

``--items``
   Restrict the run to specific checklist item numbers.

``--keep-phrases``
   Skip the cleanup of the throwaway test phrases that the script writes into a
   dedicated ``Checklist`` folder on the VM. Useful for inspecting them after a
   failure.

``--repo-path``
   The AutoKey checkout on the VM (default ``~/autokey``), used to find
   ``tests/manual/paste_probe_gtk.py`` there.

What is not automated
^^^^^^^^^^^^^^^^^^^^^

Checklist items 6 to 8 (recording a key combination by keyboard or by mouse, and
window detection by crosshair click) are not automated, and are not planned to
be. They need GTK dialog widgets (tree views, popups) to be driven with no
accessibility API available to find real widget positions, and blind navigation
proved too unreliable. Run those three by hand, following the
:ref:`manual-checklist`.

Adding a VM to the script
^^^^^^^^^^^^^^^^^^^^^^^^^

Add an entry to the ``VMS`` dictionary at the top of ``checklist.py``:

.. code-block:: python

   "myvm": {"vbox_name": "myvm", "session_proc_pattern": "gnome-shell", "wayland": True},

``vbox_name``
   The VirtualBox VM name, used by ``VBoxManage screenshotpng`` on failure. Use
   ``None`` for a machine that is not a VirtualBox VM; screenshots are then
   skipped.

``session_proc_pattern``
   A ``pgrep -f`` pattern that matches a real desktop-session process. The script
   reads ``DISPLAY``, ``XAUTHORITY``, ``DBUS_SESSION_BUS_ADDRESS`` and
   ``XDG_RUNTIME_DIR`` from its environment, because SSH sessions do not inherit
   them.

``wayland``
   ``True`` to inject input with ``ydotool``, or ``False`` to use
   ``xdotool`` and XTEST on a real X11 session.

Fresh-install .deb packaging check
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checklist items 1 to 10 assume that AutoKey is already installed from source or
pip, so they never exercise the real ``.deb`` packaging (``debian/build.sh``,
``debian/rules``, or the ``postinst`` and ``prerm`` scripts of
``autokey-common``). A bug confined to packaging, such as issue #1277, is
invisible to them. ``--fresh-install`` tests that path instead. It reverts the VM
to its clean ``installed`` snapshot, clones the repository and builds the
``.deb`` packages, installs them with ``apt`` (which runs ``postinst``), and then
removes them (which runs ``prerm``):

.. code-block:: bash

   python3 tests/vm_checklist/checklist.py --vm u24 --fresh-install
   python3 tests/vm_checklist/checklist.py --vm u24 --fresh-install --git-ref my-pr-branch --yes

``--git-ref`` chooses what to clone (default ``develop``).

.. warning::

   This discards whatever is currently running on the VM, so the script asks for
   confirmation unless you pass ``--yes``. It does **not** revert afterward; it
   leaves the VM in its post-install, post-removal state for you to inspect.
   Revert to a snapshot by hand before you reuse that VM for items 1 to 10. Those
   expect their own "AutoKey runs cleanly" snapshot, not ``installed``.

Manual probe tools
------------------

For anything the checklist script does not cover, or when you want to watch
AutoKey behave yourself, ``tests/manual/`` has four small GUI tools. Run any of
them directly on the VM's desktop, or over SSH with the session environment
forwarded.

``paste_probe.py``
   A Tk window that mirrors everything pasted into it to a file, so you can verify
   exactly what text AutoKey sent, with any paste method and on any desktop
   session. ``outfile`` defaults to ``/tmp/paste-probe.out``.

``paste_probe_gtk.py``
   The same idea, backed by a GTK ``TextView``. Use this one for mouse-selection
   (middle-click) paste. GTK's PRIMARY-selection implementation does not answer a
   Tk widget's selection request correctly, so ``paste_probe.py`` gives a false
   negative there even though the paste works. On Wayland, start it with
   ``GDK_BACKEND=x11`` so that its window is visible to X11 tools such as
   ``xdotool`` and ``wmctrl`` through XWayland.

``mouse_probe.py``
   Four labeled click targets (left, right, double, drag) that log every
   recognized event with its widget-relative and screen coordinates, for
   verifying mouse automation. ``outfile`` defaults to
   ``/tmp/mouse-probe.out``.

``gopher_hunt.py``
   A moving target for AutoKey's image-matching API (``visgrep`` and
   ``click_on_pat``). X11 only; it does not work on Wayland. A gopher sprite
   (``gopher.png``) appears at a random position and an AutoKey script has to find
   it by image match and click it. A hit adds to the score and moves the gopher; a
   miss subtracts from it. The log (default ``/tmp/gopher-hunt.log``) has one JSON
   line per spawn, hit and miss, and each hit records how long the find-and-click
   loop took, which helps you judge whether image matching is fast enough for a
   given use.

.. code-block:: bash

   python3 tests/manual/paste_probe.py [outfile]
   python3 tests/manual/paste_probe_gtk.py [outfile]
   python3 tests/manual/mouse_probe.py [outfile]
   python3 tests/manual/gopher_hunt.py [gopher.png] [logfile]

The usual loop is: focus the probe window, press Escape to clear it, trigger the
AutoKey behavior under test, then read the output file.

An example script to run against ``gopher_hunt.py`` (``highlevel`` is available
in script scope without an import):

.. code-block:: python

   import time

   for i in range(20):
       try:
           highlevel.click_on_pat("/tmp/gopher.png", mousebutton=highlevel.LEFT)
       except highlevel.PatternNotFound:
           pass
       time.sleep(0.05)

.. important::

   Keep the target window on top and unobstructed. All four tools work on real
   screen coordinates or a full-screen capture, not on a window handle. If another
   window overlaps the target, including AutoKey's own configuration window, a
   click can land on whatever is on top, and ``gopher_hunt.py`` can miss entirely
   because it never sees the sprite behind the obstruction. Bring the target
   window to the front immediately before every run, not just once at the start of
   a session.

Common problems
---------------

``apt-get update`` fails with "Release file ... is not valid yet"
   The VM's clock drifted while it was stopped. Run ``timesync.sh`` from step 10
   of creating a VM, then try again.

``wmctrl`` crashes on KDE Plasma Wayland (k26)
   Every invocation, even ``wmctrl -l``, crashed with a segmentation fault on
   Plasma 6.6.6. Use ``xdotool`` for window listing, activation and focus; it
   works on all three VMs.

``Super+H`` does nothing on KDE
   That shortcut minimizes a window on GNOME and Cinnamon but has no effect on
   KDE. On k26, close the window (AutoKey keeps running in the background) or
   move it off-screen with ``xdotool windowmove``.

A process started over SSH has no ``DISPLAY`` or clipboard access
   An SSH session does not inherit the desktop session's environment. Read the
   variables from a real session process instead of guessing:

   .. code-block:: bash

      pgrep -u autokey -f "gnome-shell\|plasmashell\|cinnamon"
      tr '\0' '\n' < /proc/<pid>/environ | grep -E "DISPLAY|XAUTHORITY|DBUS_SESSION_BUS_ADDRESS|XDG_"

   A missing ``XDG_SESSION_DESKTOP`` in particular makes AutoKey's own
   desktop detection treat a supported desktop as unsupported, even when
   everything else is right.

``xdotool key`` and ``type`` do nothing on Wayland
   ``xdotool``'s key and mouse events use the X11 XTEST protocol, which does not
   reach XWayland client windows at all, even though ``xdotool`` itself reports
   success. Use ``ydotool``, which injects through the kernel ``uinput`` device,
   the same path AutoKey's own input backend uses, for anything that has to reach
   AutoKey or a target application on u24 or k26. ``xdotool`` is still fine for
   window discovery and activation.

KDE Wayland blocks the first synthetic input with a permission dialog
   The first time anything injects input through ``uinput`` in a session, KWin
   shows a native "Remote Control" permission dialog, and more ``uinput`` input
   cannot dismiss it. Use ``ydotool`` to click through it once per session before
   you run any other check.

A screensaver swallows synthetic keystrokes
   On m22 and u24, an idle-triggered screen lock during a test absorbs every
   keystroke sent afterward, with no error. Disable idle lock before a test
   session, and take a screenshot whenever input seems to have stopped having any
   effect.

Tips
----

- **Take a screenshot without a GUI viewer.** From the host, run
  ``VBoxManage controlvm <vboxname> screenshotpng <path>`` and open the PNG. It
  is useful whenever you are not watching the VM's console.
- **Fully stop VMs between test sessions** rather than leaving them idle. A cold
  ``VBoxManage startvm <name> --type headless``, followed by polling SSH until it
  answers, gives a more predictable starting state than a VM that has been
  suspended or left running for days.
- **Use a separate git worktree for the branch under test** instead of changing a
  VM's own checkout. That avoids clobbering work in progress already in the VM's
  copy of the repository.
- **Space out independent synthetic-input calls by two to three seconds,**
  especially right after restarting the target process or while the VM is busy,
  for example in the middle of ``apt-get``. Two input events sent too close
  together can arrive interleaved and produce a key combination that never
  matches the intended hotkey. This is an input-injection race, not an AutoKey
  bug.
- **Treat KDE's two** ``ydotool`` **builds as different tools.** Depending on how
  the package was built, ``ydotool key`` takes either raw keycodes
  (``<code>:<pressed>`` pairs) or human-readable combinations (``ctrl+alt+c``),
  and the two are not interchangeable. Run ``ydotool key --help`` once on each VM
  and note which kind it is.
- **Rebuild the whole fleet now and then, on purpose.** A full rebuild from
  scratch (fresh install, new ``installed`` snapshot) clears out the one-off state
  that builds up over many test sessions. Do it periodically rather than only when
  something breaks.
- **If mouse movement on Wayland seems wrong,** check whether the pointer's
  acceleration profile is anything other than ``flat`` with ``speed 0``. GNOME's
  default adaptive profile distorts synthetic mouse movement unpredictably, which
  is confusing to debug if you do not know to look for it.
