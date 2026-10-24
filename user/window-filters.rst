Window filters
==============

A window filter limits a trigger to some windows. You can make a phrase work only
in your text editor, a hotkey only in a terminal, or an abbreviation everywhere
except in one program.

Setting a filter
----------------

1. Select the phrase, script or folder.
2. Click **Set** next to **Window Filter**.
3. Enter a regular expression, or click **Detect Window Properties** and then click
   the window you want. AutoKey shows that window's title and class, and you choose
   which one to use. It is put in the filter exactly as it is, so edit it into
   the expression you want: titles often contain characters such as parentheses
   and dots that have a special meaning in a regular expression, and the title
   usually changes, for example when you open another document.
4. Confirm, then save.

How a filter matches
--------------------

The filter is a Python regular expression. AutoKey tests it against the window's
**title** and against its **class**, and the filter matches if **either** one
matches.

.. important::

   The expression is matched from the **start** of the title or class. It does not
   search inside them. The filter ``Firefox`` matches a title that begins with
   "Firefox", but not "Mozilla Firefox" or "Inbox - Firefox". Write ``.*Firefox``
   (with a leading ``.*``) to match "Firefox" anywhere in the title.

Use **Detect Window Properties** to see the real title and class on your desktop.
On Wayland these come from the GNOME Shell extension or from KWin, so the values can
differ from what you see on X11.

Folders and inheritance
-----------------------

A filter on a folder applies to the folder's own trigger. To make it also apply to
everything inside the folder, turn on **Apply recursively to subfolders and
items**.

An item without a filter of its own inherits the recursive filter of the nearest
folder above it that has one. An item that has its own filter uses that one and
ignores the folder's.

Excluding windows: the invert option
------------------------------------

Turn on **Invert: trigger in all windows EXCEPT those matching** to flip the
filter. The trigger then works everywhere except in windows that match.

With the invert option, a window is excluded if **either** its title **or** its
class matches. This is the case a "not" expression inside the regular expression
cannot cover, because AutoKey applies the expression to the title and the class
separately.

An inverted filter inherits the same way as a normal one, together with its invert
setting.

Filters and shared triggers
---------------------------

Two items can share the same abbreviation or hotkey if their window filters differ.
For example, one hotkey can do one thing in your editor and another in your
browser. A filter and the same filter inverted count as different, because they
cover different windows. AutoKey refuses to save two items that would collide.

Examples
--------

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Filter
     - Effect
   * - ``.*Terminal.*``
     - Windows whose title or class starts with anything and contains "Terminal".
   * - ``.*LibreOffice.*`` with **Invert** on
     - Everywhere except LibreOffice windows.
   * - ``Navigator.*``
     - Windows whose title or class starts with "Navigator".

Check any filter you write with **Detect Window Properties** before you rely on it.
