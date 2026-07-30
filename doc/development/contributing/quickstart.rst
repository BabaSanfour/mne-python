.. _contributing-quickstart:

Contributor quickstart
======================

.. highlight:: console

This page covers the shortest path from choosing a task to opening a pull
request. Follow the linked pages when you need more detail.

1. Choose and discuss one change
--------------------------------

Search the `GitHub issues page`_ before starting. For a bug fix or feature,
comment on an existing issue or `open a new issue`_ to confirm the scope.
General usage and troubleshooting questions belong on `our user forum`_.
A documentation typo or similarly small correction usually does not need prior
discussion.

2. Prepare your development checkout
------------------------------------

Follow :doc:`setup` to create an isolated development environment and install
MNE-Python in editable mode, then follow :doc:`git` to fork and clone the
repository. Create a focused branch from an up-to-date ``main`` branch::

    $ git switch main
    $ git pull upstream main
    $ git switch -c descriptive-branch-name

3. Add or update a test
-----------------------

For code changes, identify the smallest existing test module that exercises the
behavior. When practical, first add a test that fails for the reported problem,
then make it pass. Prefer extending a compact existing test over adding a
separate test that repeats the same setup. See :ref:`run-tests`.

4. Implement and document the change
------------------------------------

Keep the pull request focused on one bug fix or feature. Follow
:doc:`conventions`, update public API docstrings when behavior changes, and
add or update an example or tutorial when that materially improves
discoverability.

5. Run focused checks locally
-----------------------------

Run the most relevant tests while developing, followed by the broader checks
appropriate for your change. For example::

    $ pytest path/to/test_file.py -k relevant_test --verbose
    $ pre-commit run --all-files

If documentation is affected, also follow :ref:`build-docs`. Running focused
checks locally gives faster feedback and avoids unnecessary CI use.

6. Add a changelog entry
------------------------

For a user-visible change, add a towncrier fragment as described in
:ref:`changelog-guide`. Very small documentation fixes, such as typo
corrections, do not need a fragment.

7. Open a pull request
----------------------

Push your branch to your fork and open a draft pull request against
``mne-tools/mne-python:main``. Explain the problem, the approach, and how you
tested it in your own words. Review the repository's AI-assistance policy and,
when applicable, disclose the tool used and the manner and scope of its
assistance.

Before marking the pull request ready for review, work through the
:ref:`contribution checklist <contributing-checklist>`. See
:ref:`github-workflow` for the full collaboration and review workflow.

.. include:: _links.inc
