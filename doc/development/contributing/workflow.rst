.. _contributing-workflow-overview:

Development workflow
====================

.. highlight:: console

Overview
--------

.. note:: Reminder: all contributors are expected to follow our
          `code of conduct`_.

Changes to MNE-Python are typically made by `forking`_ the MNE-Python
repository, making changes to your fork (usually by `cloning`_ it to your
personal computer, making the changes locally, and then `pushing`_ the local
changes up to your fork on GitHub), and finally creating a `pull request`_ to incorporate
your changes back into the shared "upstream" version of the codebase.

In general you'll be working with three different copies of the MNE-Python
codebase: the official remote copy at https://github.com/mne-tools/mne-python
(usually called ``upstream``), your remote `fork`_ of the upstream repository
(similar URL, but with your username in place of ``mne-tools``, and usually
called ``origin``), and the local copy of the codebase on your computer. The
typical contribution process is to:

1. synchronize your local copy with ``upstream``

2. make changes to your local copy

3. `push`_ your changes to ``origin`` (your remote fork of the upstream)

4. submit a `pull request`_ from your fork into ``upstream``

The sections :ref:`basic-git` and :ref:`github-workflow` describe this process
in more detail.

.. _`github-workflow`:

GitHub workflow
---------------

Nearly everyone in the community of MNE-Python contributors and maintainers is
a working scientist, engineer, or student who contributes to MNE-Python in
their spare time. For that reason, a set of best practices have been adopted to
streamline the collaboration and review process. Most of these practices are
common to many open-source software projects, so learning to follow them while
working on MNE-Python will bear fruit when you contribute to other projects
down the road. Here are the guidelines:

- Search the `GitHub issues page`_ (both open and closed issues) in case
  someone else has already started work on the same bugfix or feature. If you
  don't find anything, `open a new issue`_ to discuss changes with maintainers
  before starting work on your proposed changes.

- Implement only one new feature or bugfix per pull request (PR). Occasionally
  it may make sense to fix a few related bugs at once, but this makes PRs
  harder to review and test, so check with MNE-Python maintainers first before
  doing this. Avoid purely cosmetic changes to the code; they make PRs harder
  to review.

- It is usually better to make PRs *from* branches other than your main
  branch, so that you can use your main branch to easily get back to a
  working state of the code if needed (e.g., if you're working on multiple
  changes at once, or need to pull in recent changes from someone else to get
  your new feature to work properly).

- In most cases you should make PRs *into* the upstream's main branch, unless
  you are specifically asked by a maintainer to PR into another branch (e.g.,
  for backports or maintenance bugfixes to the current stable version).

- Don't forget to include in your PR a brief description of the change in a
  changelog entry (see :ref:`the changelog section <changelog-guide>` for
  instructions).

- Our community uses the following commit tags and conventions:

  - Work-in-progress PRs should be created as `draft PRs`_ and the PR title
    should begin with ``WIP``.

  - When you believe a PR is ready to be reviewed and merged, `convert it
    from a draft PR to a normal PR`_, change its title to begin with ``MRG``,
    and add a comment to the PR asking for reviews (changing the title does not
    automatically notify maintainers).

  - PRs that only affect documentation should additionally be labelled
    ``DOC``, bugfixes should be labelled ``FIX``, and new features should be
    labelled ``ENH`` (for "enhancement"). ``STY`` is used for style changes
    (i.e., improving docstring consistency or formatting without changing its
    content).

  - the following commit tags are used to interact with our
    `continuous integration`_ (CI) providers. Use them judiciously; *do not
    skip tests simply because they are failing*:

    - ``[skip circle]`` Skip `CircleCI`_, which tests successful building of
      our documentation.

    - ``[skip actions]`` Skip our `GitHub Actions`_, which test installation
      and execution on Linux and macOS systems.

    - ``[skip azp]`` Skip `azure`_ which tests installation and execution on
      Windows systems.

    - ``[ci skip]`` is an alias for ``[skip actions][skip azp][skip circle]``.
      Notice that ``[skip ci]`` is not a valid tag.

    - ``[circle full]`` triggers a "full" documentation build, i.e., all code
      in tutorials and how-to examples will be *executed* (instead of just
      nicely formatted) and the resulting output and figures will be rendered
      as part of the tutorial/example.

- Examples and tutorials should execute as quickly and with as low memory usage as
  possible while still conveying necessary information. To see current execution
  times and memory usage, visit the `sg_execution_times page`_. To see unused API
  entries, see the `sg_api_usage page`_.

`This sample pull request`_ exemplifies many of the conventions listed above:
it addresses only one problem; it started with an issue to discuss the problem
and some possible solutions; it is a PR from the user's non-main branch into
the upstream main branch; it separates different kinds of changes into
separate commits and uses labels like ``DOC``, ``FIX``, and ``STY`` to make it
easier for maintainers to review the changeset; etc. If you are new to GitHub
it can serve as a useful example of what to expect from the PR review process.

.. _taking-over-a-stale-pr:

Taking over a stale pull request
--------------------------------

If a pull request has been abandoned, someone else can take it over. A PR is generally
considered abandoned when there have been no commits for several weeks, and the original
author has not already indicated that they plan to continue at a later time. In this
case, first ping the author in a comment on the PR, asking if they intend to continue
working on it. If the original author replies that they do not intend to continue the
work, or there is no reply after at least 2 weeks, you can take over the PR.

To build on top of the original author's existing work, you can add a copy of the branch
they were working on to your fork:

.. code-block:: bash

    git remote add original-author https://github.com/original-author/mne-python.git
    git fetch original-author
    git checkout -b some-new-feature original-author/some-new-feature

You can then push any further commits to, and open a PR from, this new branch on your
fork.

Reference the original PR number in the description of the new PR (e.g., "Closes
#12345 (supersedes)"), and keep any links to the corresponding issue (e.g., "Fixes #12340").

Before the PR is merged, make sure the original author is credited for their existing
work. To credit them in the documentation, add their name to ``doc/changes/names.inc``,
if not already included, and add them as an author in the changelog entry, e.g.:

.. code-block:: rst

    Short description of the changes, by `Your Name`_ and `Original Author Name`_.

To credit the original author in the code, add their name and email as a co-author to
the end of at least one commit message (e.g., the commit that adds the changelog
entry):

.. code-block:: rst

    Your commit message
    
    Co-authored-by: Original Author Name <original-author-email@example.com>


.. include:: _links.inc
