Testing your changes
====================

AutoKey has two layers of testing. The automated tests are fast and run
anywhere, but they mock out the display server, the input devices and the
desktop-specific IPC (D-Bus and KWin scripting), so they cannot catch bugs that
only appear when AutoKey drives a real X11 or Wayland session. The manual
checklist covers those, and :doc:`vm-testing` explains how to run it, and an
automated version of it, on virtual machines.

.. note::

   The maintainers keep the working copies of this material in the AutoKey
   repository:
   `CONTRIBUTORS.rst <https://github.com/autokey/autokey/blob/develop/CONTRIBUTORS.rst>`__
   and
   `MANUAL_TESTING.rst <https://github.com/autokey/autokey/blob/develop/MANUAL_TESTING.rst>`__.

Automated tests
---------------

Tests must pass before you submit a PR. Install ``tox`` through ``pip``, or
``python-tox`` through your package manager. Tox installs pytest and PyHamcrest
in its own virtual environment when it runs, so you do not need to install them
yourself.

.. list-table::
   :header-rows: 1
   :widths: 45 55

   * - Command
     - What it does
   * - ``tox``
     - Runs the whole test suite.
   * - ``tox -- tests/[test file] [--opt]``
     - Runs individual test files or folders, or passes options to pytest.
   * - ``tox -e lint``
     - Lints the project with flake8.
   * - ``tox -e clean,coverage,report``
     - Generates test coverage reports.

The lint step fails the build only on the flake8 checks ``E9``, ``F63``, ``F7``
and ``F82``, with a maximum complexity of 10 and a maximum line length of 127,
on ``lib/autokey``. See ``setup.cfg`` in the AutoKey repository.

The ``tests`` directory on ``master`` belongs to the old 0.96.x code and is
deprecated. Run the tests from the ``develop`` branch.

Running the tests before every push
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To run the automated tests before every push, you can add these lines to the file
``.git/hooks/pre-push`` in your clone:

.. code-block:: sh

   #!/bin/sh
   tox
   exit $?

This aborts a push to your fork if any test fails.

Running the CI workflow locally
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To reproduce a GitHub Actions run without pushing and waiting, you can use
`act <https://github.com/nektos/act>`__. From the root of the AutoKey clone:

.. code-block:: bash

   act pull_request -j pytest \
       -P ubuntu-latest=catthehacker/ubuntu:act-latest \
       --matrix python-version:3.12 \
       -W .github/workflows/python-test.yml

The CI workflow tests Python 3.10 to 3.14.

Debugging hung and intermittent tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For a hung test, get a real Python thread stack trace with
``py-spy dump --pid <pid>`` instead of guessing from timestamps. Intermittent,
race-condition bugs need volume to confirm: run the suspect path 20 to 40 times
and count how often it fails, because a single clean run, or a single failure,
proves little.

.. _manual-checklist:

Manual verification
-------------------

Automated and manual tests can be run on your own machine, but they should also
be run on fresh VMs (see :doc:`vm-testing`), so that quirks of your local
environment do not hide build problems.

The manual checklist is required for PRs that change AutoKey's runtime
behavior. Purely cosmetic changes do not need it.

When to use it
^^^^^^^^^^^^^^

Run the checklist when a PR touches:

- keyboard or mouse input handling,
- clipboard operations,
- window detection or manipulation,
- hotkey or abbreviation triggering, or
- desktop-integration code: notifiers, tray icons, KWin or GNOME extensions.

How to compare
^^^^^^^^^^^^^^

Where practical, check out ``master``, ``develop`` and the PR branch as three
separate installs. A plain ``pip install -e .`` in a virtual environment is
often *not* enough, because some problems, such as desktop-launcher hooks, only
appear in a full package install. Reproduce the reported behavior on the base
branch first, then confirm that the PR branch fixes it or at least does not
regress it.

Environments
^^^^^^^^^^^^

Test on whichever of these the PR's changes touch:

- X11 (any traditional X11 desktop)
- GNOME on Wayland
- KDE Plasma on Wayland

Core checklist
^^^^^^^^^^^^^^

For each environment that applies:

1. **Phrase expansion via abbreviation.** Type a configured abbreviation and
   confirm that it expands.
2. **Phrase or script trigger via global hotkey.** Confirm that the hotkey fires
   the correct phrase or script.
3. **Paste method: keyboard.** Send a phrase with simulated keystrokes and
   confirm the correct text arrives in the target application.
4. **Paste method: clipboard (Ctrl+V).** Confirm that the phrase lands on the
   system clipboard and pastes, and that the original clipboard contents are
   restored afterward.
5. **Paste method: mouse selection (middle-click).** Confirm that the phrase is
   pasted through the PRIMARY selection.
6. **Record a key combination, keyboard-triggered.** Open the dialog with the
   keyboard and confirm that the recorded key is correct.
7. **Record a key combination, mouse-triggered.** Open the dialog by clicking a
   button and confirm that the click is not recorded as the key.
8. **Window detection (crosshair click-to-select).** Confirm that it identifies
   both X11 and native Wayland target windows, where applicable.
9. **Enable/disable expansions toggle.** From the tray icon or notifier menu,
   confirm that expansions stop and resume.
10. **Config GUI sanity.** Open the relevant GTK or Qt configuration window and
    confirm that it does not crash and that settings persist.

This list is a baseline, not an exhaustive one. Add or adjust steps for what the
specific PR changes.

Adding new scenarios
^^^^^^^^^^^^^^^^^^^^

If you find a bug that this checklist would not have caught, or you know of an
interaction path worth covering, please contribute a PR to
`MANUAL_TESTING.rst <https://github.com/autokey/autokey/blob/develop/MANUAL_TESTING.rst>`__.
