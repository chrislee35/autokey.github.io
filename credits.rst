Credits
=======

AutoKey exists because many people gave their time to it. This page thanks the
people who build it, test it and use it, and the projects it stands on.

Maintainers
-----------

Thank you to the people who review pull requests, answer issues, cut releases
and keep AutoKey working as desktops change:

- `josephj11 <https://github.com/josephj11>`__
- `Elliria <https://github.com/Elliria>`__
- `troxor <https://github.com/troxor>`__
- `dlk3 <https://github.com/dlk3>`__

Founders
--------

AutoKey was created by Sam Peterson (`peabody17 <https://github.com/peabody17>`__),
who was generous enough to let others join the project and take it forward.
Thank you also to Eddie Bell, whose keylogger example gave Sam the way to
monitor keyboard input, and to the authors of Textpander, Texter and AutoHotkey,
whose software inspired AutoKey.

Contributors
------------

Thank you to everyone who has sent code, documentation, bug reports and
fixes to AutoKey. These are the people listed by GitHub as
contributors to the `autokey/autokey <https://github.com/autokey/autokey/graphs/contributors>`__
repository:

`luziferius <https://github.com/luziferius>`__,
`troxor <https://github.com/troxor>`__,
`Elliria <https://github.com/Elliria>`__,
`BlueDrink9 <https://github.com/BlueDrink9>`__,
`josephj11 <https://github.com/josephj11>`__,
`sebastiansam55 <https://github.com/sebastiansam55>`__,
`guoci <https://github.com/guoci>`__,
`K0Stek122 <https://github.com/K0Stek122>`__,
`dogweather <https://github.com/dogweather>`__,
`userdocs <https://github.com/userdocs>`__,
`xpusostomos <https://github.com/xpusostomos>`__,
`anthonyfok <https://github.com/anthonyfok>`__,
`HactarCE <https://github.com/HactarCE>`__,
`tblancher <https://github.com/tblancher>`__,
`sporkwitch <https://github.com/sporkwitch>`__,
`Cyb10101 <https://github.com/Cyb10101>`__,
`denis-roy <https://github.com/denis-roy>`__,
`HyperHamster <https://github.com/HyperHamster>`__,
`jspaetzel <https://github.com/jspaetzel>`__,
`kjs3 <https://github.com/kjs3>`__,
`ldorigo <https://github.com/ldorigo>`__,
`mikesmitty <https://github.com/mikesmitty>`__,
`founderio <https://github.com/founderio>`__,
`pedropenna <https://github.com/pedropenna>`__,
`raags <https://github.com/raags>`__,
`keremispirli <https://github.com/keremispirli>`__,
`trxcllnt <https://github.com/trxcllnt>`__,
`vinnyA3 <https://github.com/vinnyA3>`__,
and `chrislee35 <https://github.com/chrislee35>`__.

Testers
-------

Thank you to everyone who installed a beta, ran AutoKey on a desktop or
distribution we had not tried, wrote down exactly what happened, and came back
to retest the fix. Wayland support in particular depended on people trying it
on real machines. In the early days, the Ubuntu forums community gave Sam the
feedback that shaped the program.

Artwork and inspiration
-----------------------

- The AutoKey icon is based on a design from the Faenza icon set by tiheum.
- The idea of changing the keyboard map on the fly to type unmapped characters
  comes from `xdotool <https://github.com/jordansissel/xdotool>`__.
- The layout of the configuration window was inspired by Phrase Express.
- Thank you to everyone who has donated to the project.

Software AutoKey is built on
----------------------------

AutoKey relies on the work of many other projects. Thank you to their
maintainers.

**Language and runtime**

- `Python <https://www.python.org/>`__

**User interface**

- `PyGObject <https://pygobject.gnome.org/>`__ and `GTK <https://www.gtk.org/>`__
  for the GTK front end
- `PyQt5 <https://www.riverbankcomputing.com/software/pyqt/>`__, which includes
  the QScintilla editor, for the Qt front end
- `Zenity <https://gitlab.gnome.org/GNOME/zenity>`__ and
  `KDialog <https://invent.kde.org/utilities/kdialog>`__ for script dialogs

**Keyboard, mouse and window handling**

- `python-xlib <https://github.com/python-xlib/python-xlib>`__ for X11
- `python-evdev <https://github.com/gvalkov/python-evdev>`__ and
  `pyudev <https://github.com/pyudev/pyudev>`__ for input devices on Wayland
- `wmctrl <https://sites.google.com/site/tstyblo/wmctrl>`__ for window
  management on X11
- `xautomation <https://www.hoopajoo.net/projects/xautomation.html>`__ for
  screen pattern matching

**System integration**

- `dbus-python <https://dbus.freedesktop.org/doc/dbus-python/>`__ and
  `pydbus <https://github.com/LEW21/pydbus>`__ for talking to the desktop
- `pyinotify <https://github.com/seb-m/pyinotify>`__ for watching your script
  and phrase folders
- `python-magic <https://github.com/ahupp/python-magic>`__ for identifying file
  types
- `xclip <https://github.com/astrand/xclip>`__ and
  `wl-clipboard <https://github.com/bugaevc/wl-clipboard>`__ for the clipboard

**Testing and documentation**

- `pytest <https://pytest.org/>`__,
  `PyHamcrest <https://github.com/hamcrest/PyHamcrest>`__,
  `tox <https://tox.wiki/>`__ and
  `flake8 <https://flake8.pycqa.org/>`__
- `act <https://github.com/nektos/act>`__ for running GitHub Actions locally
- `Sphinx <https://www.sphinx-doc.org/>`__ and the
  `Read the Docs theme <https://github.com/readthedocs/sphinx_rtd_theme>`__ that
  build this site, and `GitHub Pages <https://pages.github.com/>`__ that hosts it

Are we missing you?
-------------------

If you have helped AutoKey and your name is not here, that is our mistake, not a
judgment. Please open an issue on the
`documentation repository <https://github.com/autokey/autokey.github.io/issues>`__
and we will add you.
