.. _contributing-checklist:

Contribution checklist
======================

Use this checklist before requesting review. The linked pages contain the
details; this page is intentionally brief.

Before coding
-------------

- [ ] I searched open and closed issues and discussed non-trivial changes with
      maintainers.
- [ ] My branch starts from a recent ``upstream/main`` and addresses one
      focused change.
- [ ] I identified the relevant existing tests, documentation, and coding
      conventions.

Before opening a pull request
-----------------------------

- [ ] I added or updated tests for changed behavior without duplicating
      existing coverage.
- [ ] I ran the smallest relevant test selection locally and it passes
      (:ref:`run-tests`).
- [ ] I ran ``pre-commit run --all-files`` and addressed failures caused by my
      changes.
- [ ] I updated docstrings and user documentation where needed, and built
      affected documentation locally (:ref:`build-docs`).
- [ ] I added a towncrier fragment for a user-visible change
      (:ref:`changelog-guide`).
- [ ] I reviewed every change and can explain its purpose and scientific or
      technical reasoning.

Before requesting review
------------------------

- [ ] The pull request description explains the problem, solution, and local
      validation in my own words.
- [ ] I disclosed the tool, manner, and scope of any AI assistance, as required
      by the repository's ``CONTRIBUTING.md`` policy.
- [ ] CI failures caused by my changes are resolved, and unrelated failures are
      identified clearly.
- [ ] The branch contains no unrelated formatting, generated files, or
      debugging changes.
- [ ] The pull request is marked ready only when it is complete enough for
      maintainer review.

For the full process, see :ref:`github-workflow`.

.. include:: _links.inc
