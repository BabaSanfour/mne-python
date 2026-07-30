.. _contributing-testing:

Testing and documentation
=========================

.. highlight:: console

Continuous integration (CI) and local testing before opening a PR
-----------------------------------------------------------------

Continuous integration (CI) uses automated systems to run tests and checks on your code
whenever you open or update a pull request.
MNE-Python uses `continuous integration`_ (CI) to ensure code quality,
test across multiple platforms, and automatically validate pull requests.
However, CI runs are slower than testing locally and some of them cost money to run.
Therefore, *do not rely on the CIs to catch bugs and style errors for you*; 
:ref:`run the tests locally <run-tests>`
instead before opening a new PR and before each time you push additional
changes to an already-open PR.

MNE-Python uses the following CI services:

- `GitHub Actions`_ and `Azure`_ for testing code across platforms
- `CodeCov`_ checks how much of the code is tested
- `CircleCI`_ for building the documentation

If you are contributing for the first time, you may notice that `Github Actions`_
jobs do not start automatically, or appear as "pending".
This is expected behavior. Github Actions requires **manual approval from a
maintainer** before running jobs submitted by new contributors. This is a
security measure to prevent misuse of CI resources.

As a result:

- Github Actions jobs may remain in a *pending* or *not running* state
- This does **not** indicate a problem with your code

Once you have at least one PR merged into the MNE-Python repository, future
contributions will not require manual approval.

`CircleCI`_ will not build the documentation unless the GitHub account of the PR's most recent commit
is associated with a CircleCI account. Creating one is easy and free, 
choose "login with GitHub" on `CircleCI`_ to get started.
If you do not do this, it will show up as a failing CI job.

Before opening or updating a PR, check locally:

- that all tests pass (see :ref:`run-tests`)
- the documentation is built without errors if your changes affect it (see :ref:`build-docs`)
- style checks pass (see :ref:`code-style`)

Make tests fast and thorough
----------------------------

Whenever possible, use the testing dataset rather than one of the sample
datasets when writing tests; it includes small versions of most MNE-Python
objects (e.g., `~mne.io.Raw` objects with short durations and few
channels). You can also check which lines are missed by the tests, then modify
existing tests (or write new ones) to target the missed lines. Here's an
example that reports which lines within ``mne.viz`` are missed when running
:file:`test_evoked.py` and :file:`test_topo.py`::

    $ pytest --cov=mne.viz --cov-report=term-missing mne/viz/tests/test_evoked.py mne/viz/tests/test_topo.py

You can also use ``pytest --durations=5`` to ensure new or modified tests will
not slow down the test suite too much.

.. _run-tests:

Running the test suite
----------------------

.. admonition:: pytest flags
    :class: sidebar tip

    The ``-x`` flag exits the pytest run when any test fails; this can speed
    up debugging when running all tests in a file or module.

    The ``--pdb`` flag will automatically start the python debugger upon test
    failure.

The full test suite can be run by calling ``pytest -m "not ultraslowtest" mne`` from the
``mne-python`` root folder. Testing the entire module can be quite
slow, however, so to run individual tests while working on a new feature, you
can run the following line::

    $ pytest mne/tests/test_evoked.py::test_io_evoked --verbose

Or alternatively::

    $ pytest mne/tests/test_evoked.py -k test_io_evoked --verbose

Make sure you have the testing dataset, which you can get by running this in
a Python interpreter:

.. code-block:: python

    >>> mne.datasets.testing.data_path(verbose=True)  # doctest: +SKIP

.. _build-docs:

Building the documentation
--------------------------

Our documentation (including docstrings in code files) is in
reStructuredText_ format and is built using Sphinx_ and `Sphinx-Gallery`_.
The easiest way to ensure that your contributions to the documentation are
properly formatted is to follow :doc:`conventions`, imitate
existing documentation examples, refer to the Sphinx and Sphinx-Gallery
reference materials when unsure how to format your contributions, and build the
docs locally to confirm that everything looks correct before submitting the
changes in a pull request.

You can build the documentation locally using `GNU Make`_ with
:file:`doc/Makefile`. From within the :file:`doc` directory, you can test
formatting and linking by running::

    $ make html-noplot

This will build the documentation *except* it will format (but not execute) the
tutorial and example files. If you have created or modified an example or
tutorial, you should instead run
:samp:`make html-pattern PATTERN={<REGEX_TO_SELECT_MY_TUTORIAL>}` to render
all the documentation and additionally execute just your example or tutorial
(so you can make sure it runs successfully and generates the output / figures
you expect).

After either of these commands completes, ``make show`` will open the
locally-rendered documentation site in your browser. If you see many warnings
that seem unrelated to your contributions, it might be that your output folder
for the documentation build contains old, now irrelevant, files. Running
``make clean`` will clean those up. Additional ``make`` recipes are available;
run ``make help`` from the :file:`doc` directory or consult the
`Sphinx-Gallery`_ documentation for additional details.

Modifying command-line tools
----------------------------

MNE-Python provides support for a limited set of :ref:`python_commands`.
These are typically used with a call like::

    $ mne browse_raw ~/mne_data/MNE-sample-data/MEG/sample/sample_audvis_raw.fif

These are generally available for convenience, and can be useful for quick
debugging (in this case, for `mne.io.Raw.plot`).

If a given command-line function fails, they can also be executed as part of
the ``mne`` module with ``python -m``. For example::

    $ python -i -m mne browse_raw ...

Because this was launched with ``python -i``, once the script completes
it will drop to a Python terminal. This is useful when there are errors,
because then you can drop into a :func:`post-mortem debugger <python:pdb.pm>`:

.. code-block:: python

    >>> import pdb; pdb.pm()  # doctest:+SKIP

.. include:: _links.inc
