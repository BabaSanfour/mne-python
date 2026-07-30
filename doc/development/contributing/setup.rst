.. _contributing-setup:

Development environment
=======================

.. highlight:: console

GNU Make
--------

We use `GNU Make`_ to organize commands or short scripts that are often needed
in development. These are stored in files with the name :file:`Makefile`.
MNE-Python has two Makefiles, one in the package's root directory (containing
mainly testing commands) and one in :file:`doc/` (containing recipes for
building our documentation pages in different ways).

To check if make is already installed type ::

   $ make

into a terminal and you should see ::

   make: *** No targets specified and no makefile found.  Stop.

If you don't see this or something similar, you may not have ``make`` installed.

.. tab-set::

    .. tab-item:: Linux
        :class-content: text-center

        .. button-link:: https://www.gnu.org/software/make/
            :ref-type: ref
            :color: primary
            :shadow:
            :class: font-weight-bold mt-3

            |cloud-arrow-down| |ensp| Get make for Linux

    .. tab-item:: macOS
        :class-content: text-center

        .. button-link:: https://www.gnu.org/software/make/
            :ref-type: ref
            :color: primary
            :shadow:
            :class: font-weight-bold mt-3

            |cloud-arrow-down| |ensp| Get make for macOS

    .. tab-item:: Windows

        If you see: ::

            bash: make: command not found

        Install ``make`` for git BASH (which comes with `git for Windows`_):

        1. Download :file:`make-{newest.version}-without-guile-w32-bin.zip` from `ezwinports`_
        2. Extract zip-folder
        3. Copy the contents into :file:`{path_to_git}\\mingw64\\` (e.g. by merging the
           folders with the equivalent ones already inside)
        4. For the first time using git BASH, you need to run once (to be able to
           activate your ``mnedev`` environment): ::

            $ conda init bash

        If instead you see an error like: ::

                bash: conda: command not found

        at the top of your git BASH window, you need to add

        - :file:`{path_to_Anaconda}`
        - :file:`{path_to_Anaconda}\\Scripts`

        to Windows-PATH first.

Creating the virtual environment
--------------------------------

.. admonition:: Supported Python environments
    :class: sidebar note

    We strongly recommend the `Anaconda`_ or `Miniconda`_ environment managers
    for Python. Other setups are possible but are not officially supported by
    the MNE-Python development team; see discussion :ref:`here
    <other-py-distros>`. These instructions use  ``conda`` where possible;
    experts may replace those lines with some combination of ``git`` and
    ``pip``.

These instructions will set up a Python environment that is separated from your
system-level Python and any other managed Python environments on your computer.
This lets you switch between different versions of Python and also switch between
the stable and development
versions of MNE-Python (so you can, for example, use the same computer to
analyze your data with the stable release, and also work with the latest
development version to fix bugs or add new features). Even if you've already
followed the :ref:`installation instructions <install-python>` for the stable
version of MNE-Python, you should now repeat that process to create a new,
separate environment for MNE-Python development (here we'll give it the name
``mnedev``)::

    $ curl --remote-name https://raw.githubusercontent.com/mne-tools/mne-python/main/environment.yml
    $ conda env create --file environment.yml --name mnedev
    $ conda activate mnedev

Now you'll have *two* MNE-Python environments: ``mne`` (or whatever custom
name you used when installing the stable version of MNE-Python) and ``mnedev``
that we just created. At this point ``mnedev`` also has the stable version of
MNE-Python (that's what the :file:`environment.yml` file installs), but we're
about to remove the stable version from ``mnedev`` and replace it with the
development version. To do that, we'll `clone`_ the MNE-Python repository from
your remote fork, and also connect the local copy to the ``upstream`` version
of the codebase, so you can stay up-to-date with changes from other
contributors. First, edit these two variables for your situation::

    $ GITHUB_USERNAME="insert_your_actual_GitHub_username_here"
    $ # pick where to put your local copy of MNE-Python development version:
    $ INSTALL_LOCATION="/opt"

.. note::
   On Windows, add ``set`` before the variable names (``set GITHUB_USERNAME=...``, etc.).

Then make a local clone of your remote fork (``origin``)::

    $ cd $INSTALL_LOCATION
    $ git clone https://github.com/$GITHUB_USERNAME/mne-python.git

Finally, set up a link between your local clone and the official repository
(``upstream``) and set up ``git diff`` to work properly::

    $ cd mne-python
    $ git remote add upstream https://github.com/mne-tools/mne-python.git
    $ git fetch --all
    $ git config --local blame.ignoreRevsFile .git-blame-ignore-revs

Now we'll remove the *stable* version of MNE-Python and replace it with the
*development* version (the clone we just created with git). Make sure you're in
the correct environment first (``conda activate mnedev``), and then do::

    $ cd $INSTALL_LOCATION/mne-python    # make sure we're in the right folder
    $ conda remove --force mne-base  # the --force avoids dependency checking
    $ pip install -e .

The command ``pip install -e .`` installs a python module into the current
environment by creating a link to the source code directory (instead of copying
the code to pip's :file:`site_packages` directory, which is what normally
happens). This means that any edits you make to the MNE-Python source code will
be reflected the next time you open a Python interpreter and ``import mne``
(the ``-e`` flag of ``pip`` stands for an "editable" installation).

Finally, we'll add a few dependencies that are not needed for running
MNE-Python, but are needed for locally running our test suite::

    $ pip install --group=test

And for building our documentation::

    $ pip install --group=doc
    $ conda install graphviz

.. note::
   On Windows, if you installed graphviz using the conda command above but still get an error like this::

      WARNING: dot command 'dot' cannot be run (needed for graphviz output), check the graphviz_dot setting

   try adding the graphviz folder to path::

      $ PATH=$CONDA_PREFIX\\Library\\bin\\graphviz:$PATH

To build documentation, you will also require `optipng`_:

- On Linux, use the command ``sudo apt install optipng``.

- On MacOS, optipng can be installed using Homebrew.

- On Windows, unzip :file:`optipng.exe` from the `optipng for Windows`_ archive
  into the :file:`doc/` folder. This step is optional for Windows users.

There are additional optional dependencies needed to run various tests, such as
scikit-learn for decoding tests, or nibabel for MRI tests. If you want to run all the
tests, consider using our MNE installers (which provide these dependencies) or pay
attention to the skips that ``pytest`` reports and install the relevant libraries.
For example, this traceback::

    SKIPPED [2] mne/io/eyelink/tests/test_eyelink.py:14: could not import 'pandas': No module named 'pandas'

indicates that ``pandas`` needs to be installed in order to run the Eyelink tests.

.. include:: _links.inc
