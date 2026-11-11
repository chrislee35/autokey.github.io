Contributing to AutoKey
=======================

You are free to take virtually any task you wish, whether that is a new feature
or a fix. Open a pull request (PR) for discussion, and the maintainers will try
to answer any questions that come up. Before you start, read
:ref:`maintainer-advice` below: it explains what the maintainers need from a
contributor.

.. note::

   The maintainers keep the working copy of this material in the AutoKey
   repository, where it is the authority if the two ever differ:
   `CONTRIBUTORS.rst <https://github.com/autokey/autokey/blob/develop/CONTRIBUTORS.rst>`__.

Reporting a security problem
----------------------------

Do not report security vulnerabilities in a public issue. Use GitHub's private
vulnerability reporting instead: open the
`Security tab <https://github.com/autokey/autokey/security>`__ of the AutoKey
repository and choose **Report a vulnerability**. Only the maintainers can see
the report. Include the AutoKey version, your distribution and desktop
environment, and the steps needed to reproduce the problem. See
`SECURITY.md <https://github.com/autokey/autokey/blob/develop/SECURITY.md>`__.

For anything that is not a security problem, use the
`issue tracker <https://github.com/autokey/autokey/issues>`__.

Setting up the development environment
--------------------------------------

1. Create a personal fork of the
   `autokey repository <https://github.com/autokey/autokey>`__.
2. Create a new branch based on ``develop`` to make your changes in. Base it on
   ``master`` only if there is a compelling reason to change ``master`` between
   releases.
3. Install AutoKey from a clone of your branch, following :ref:`install-source`,
   and install the dependencies listed there.

Making your changes
-------------------

Make your changes in your local clone and run AutoKey from it, repeating until
you are satisfied with the result.

- **Do not edit CHANGELOG.rst** in your branch. The maintainers update it in
  batches, in separate changelog PRs, from the titles and descriptions of the
  PRs that have been merged. A clear title and description is what makes that
  possible.
- **If you change the scripting API,** run ``python3 extractDoc.py`` and add the
  result to your commit. It regenerates the autocompletion files that the Qt and
  GTK editors use; see :doc:`internals`. It does not generate the published
  documentation.
- **Do not edit the published documentation in a code PR.** The user and API
  documentation (this site) lives in the separate
  `autokey.github.io <https://github.com/autokey/autokey.github.io>`__
  repository, and the maintainers rebuild it. If you have ideas for the
  documentation, open an issue or a discussion thread, or add tips to the wiki.

When you are done, push your changes to your fork on GitHub.

Testing your changes
--------------------

Tests must pass before you submit a PR. At a minimum, run the automated tests in
your local clone. A PR that changes how AutoKey behaves in a real session also
needs the manual checks, ideally on fresh virtual machines. See :doc:`testing`
and :doc:`vm-testing`.

Making a pull request
---------------------

When your testing is done and your changes are in your fork, open a pull
request from your branch to the target branch of AutoKey, usually ``develop``.

GitHub Actions runs the tests on pull requests to the ``master``, ``develop``
and ``beta`` branches. Tagged releases merged into ``develop``, ``beta`` and
``master`` are built automatically.

Running your own version in place
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It can be convenient to make your ``pip`` installation of AutoKey use your
source folder. ``cd`` to your ``autokey`` source folder and install with
``pip3 install -e .``. The scripts that pip puts in your ``PATH`` then run
whatever changes you make to the source.

Reviewing and merging
---------------------

For maintainers and reviewers:

- Before merging a PR that changes AutoKey's runtime behavior, verify it by
  running the affected code path, ideally comparing behavior across ``master``,
  ``develop`` and the PR branch, rather than relying on code review alone.
  Purely cosmetic changes (comments, dead code, debug output, formatting) do not
  need this level of verification. The :ref:`manual-checklist` is the baseline.
- Do not merge a PR while another maintainer has an open, unresolved concern on
  it without checking with them first. Either resolve the concern together, or
  get their explicit agreement to proceed anyway.

.. _maintainer-advice:

A maintainer's advice on contributing
-------------------------------------

One of the maintainers wrote this note for people thinking about sending a PR.
It draws on Martin Šošić's article,
`A maintainer's advice on contributing to open source in 2026
<https://dev.to/wasp/a-maintainers-advice-on-contributing-to-open-source-in-2026-4ea1>`__,
which they agree with almost entirely. Šošić is a maintainer of the Wasp
project.

The best contributors are people who use AutoKey and want to make it better, and
a few have stayed for years. For every one of those there are plenty of drive-by
PRs written for a résumé line or a contest. AI has made it easy to produce a
polished, complicated PR from someone who does not understand it, so a big,
shiny PR no longer shows that anyone put in effort. What a maintainer needs now
is to trust the person behind the PR. You can help by being an honest human:

- **Say why you care.** Tell the maintainers why you care about AutoKey and how
  you use it.
- **Write in your own words.** Write your PR description yourself, even if it is
  a bit rough. A typo is better than wondering whether anyone is there.
- **Own every line.** You have run it, and you can explain and defend each
  decision as if no AI had been involved.
- **Start small.** Pick something easy to review, and say which issue you want to
  take before you start on it.

The maintainers review changes on their own time, so make it easy for them. Do
that and they will gladly work with you.
