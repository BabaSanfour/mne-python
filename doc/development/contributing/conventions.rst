.. _contributing-conventions:

Coding conventions
==================

.. highlight:: console

General requirements
--------------------

All new functionality must have test coverage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For example, a new `mne.Evoked` method in :file:`mne/evoked.py` should
have a corresponding test in :file:`mne/tests/test_evoked.py`.


All new functionality must be documented
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This includes thorough docstring descriptions for all public API changes, as
well as how-to examples or longer tutorials for major contributions. Docstrings
for private functions may be more sparse, but should usually not be omitted.


Avoid API changes when possible
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Changes to the public API (e.g., class/function/method names and signatures)
should not be made lightly, as they can break existing user scripts. Changes to
the API require a deprecation cycle (with warnings) so that users have time to
adapt their code before API changes become default behavior. See :ref:`the
deprecation section <deprecating>` and `mne.utils.deprecated` for
instructions. Bug fixes (when something isn't doing what it says it will do) do
not require a deprecation cycle.

Note that any new API elements should be added to the main reference;
classes, functions, methods, and attributes cannot be cross-referenced unless
they are included in the :ref:`api_reference`
(:file:`doc/python_reference.rst`).


.. _deprecating:

Deprecate with a decorator or a warning
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MNE-Python has a :func:`~mne.utils.deprecated` decorator for classes and
functions that will be removed in a future version:

.. code-block:: python

    from mne.utils import deprecated

    @deprecated('my_function is deprecated and will be removed in 0.XX; please '
                'use my_new_function instead.')
    def my_function():
       return 'foo'

If you need to deprecate a parameter, use :func:`mne.utils.warn`. For example,
to rename a parameter from ``old_param`` to ``new_param`` you can do something
like this:

.. code-block:: python

    from mne.utils import warn

    def my_other_function(new_param=None, old_param=None):
        if old_param is not None:
            depr_message = ('old_param is deprecated and will be replaced by '
                            'new_param in 0.XX.')
            if new_param is None:
                new_param = old_param
                warn(depr_message, FutureWarning)
            else:
                warn(depr_message + ' Since you passed values for both '
                     'old_param and new_param, old_param will be ignored.',
                     FutureWarning)
        # Do whatever you have to do with new_param
        return 'foo'

When deprecating, you should also add corresponding test(s) to the relevant
test file(s), to make sure that the warning(s) are being issued in the
conditions you expect:

.. code-block:: python

    # test deprecation warning for function
    with pytest.warns(FutureWarning, match='my_function is deprecated'):
        my_function()

    # test deprecation warning for parameter
    with pytest.warns(FutureWarning, match='values for both old_param'):
        my_other_function(new_param=1, old_param=2)
    with pytest.warns(FutureWarning, match='old_param is deprecated and'):
        my_other_function(old_param=2)

You should also search the codebase for any cases where the deprecated function
or parameter are being used internally, and update them immediately (don't wait
to the *end* of the deprecation cycle to do this). Later, at the end of the
deprecation period when the stated release is being prepared:

- delete the deprecated functions
- remove the deprecated parameters (along with the conditional branches of
  ``my_other_function`` that handle the presence of ``old_param``)
- remove the deprecation tests
- double-check for any other tests that relied on the deprecated test or
  parameter, and (if found) update them to use the new function / parameter.


.. _`changelog-guide`:

Describe your changes in the changelog
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Include in your changeset a brief description of the changes, which will appear in the
:ref:`changelog <whats_new>`, using towncrier_ format. This aggregates small,
properly named ``.rst`` files to create a changelog. This can be
skipped for very minor changes like correcting typos in the documentation.

There are six separate sections for changes, based on change type.
To add a changelog entry to a given section, name it as
:file:`doc/changes/dev/<PR-number>.<type>.rst`. The types are:

notable
    For overarching changes, e.g., adding type hints package-wide. These are rare.
dependency
    For changes to dependencies, e.g., adding a new dependency or changing
    the minimum version of an existing dependency.
bugfix
    For bug fixes. Can change code behavior with no deprecation period.
apichange
    Changes to existing code behavior that require a deprecation period. Can include the
    addition of new features only if existing code behavior is changed alongside this.
newfeature
    For new features that do not change existing code behavior. If existing code
    behavior is changed alongside the addition of these features, the changes should
    instead be labelled as ``apichange``.
other
    For changes that don't fit into any of the above categories, e.g.,
    internal refactorings.

For example, for a new feature PR with number 12345, the changelog entry should be
added as a new file :file:`doc/changes/dev/12345.newfeature.rst`. The file should
contain:

1. A brief description of the change, typically in a single line of one or two
   sentences.
2. reST links to **public** API endpoints like functions (``:func:``),
   classes (``:class:``), and methods (``:meth:``). If changes are only internal
   to private functions/attributes, mention internal refactoring rather than name
   the private attributes changed.
3. Author credit. If you are a new contributor (we're very happy to have you here! 🤗),
   you should using the ``:newcontrib:`` reST role, whereas previous contributors should
   use a standard reST link to their name. For example, a new contributor could write:

   .. code-block:: rst

      Short description of the changes, by :newcontrib:`Firstname Lastname`.

   And an previous contributor could write:

   .. code-block:: rst

      Short description of the changes, by `Firstname Lastname`_.

Make sure that your name is included in the list of authors in
:file:`doc/changes/names.inc`, otherwise the documentation build will fail.
To add an author name, append a line with the following pattern (note
how the syntax is different from that used in the changelog):

.. code-block:: rst

  .. _Your Name: https://www.your-website.com/

Many contributors opt to link to their GitHub profile that way. Have a look
at the existing entries in the file to get some inspiration.

Sometimes, changes that shall appear as a single changelog entry are spread out
across multiple PRs. In this case, edit the existing towncrier file for the relevant
change, and append additional PR numbers in parentheticals with the ``:gh:`` role like:

.. code-block:: rst

    Short description of the changes, by `Firstname Lastname`_. (:gh:`12346`)



Code style
----------

Adhere to standard Python style guidelines
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

All contributions to MNE-Python are checked against style guidelines described
in `PEP 8`_. We also check for common coding errors (such as variables that are
defined but never used). We allow very few exceptions to these guidelines, and
use tools such as ruff_ to check code style
automatically. From the :file:`mne-python` root directory, you can check for
style violations by first installing our pre-commit hook::

    $ pip install pre-commit
    $ pre-commit install --install-hooks

Then running::

    $ make ruff  # alias for `pre-commit run -a`

in the shell. Several text editors or IDEs also have Python style checking,
which can highlight style errors while you code (and train you to make those
errors less frequently). This functionality is built-in to the Spyder_ IDE, but
most editors have plug-ins that provide similar functionality. Search for
:samp:`python linter <name of your favorite editor>` to learn more.


Use consistent variable naming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Classes should be named using ``CamelCase``. Functions and instances/variables
should use ``snake_case`` (``n_samples`` rather than ``nsamples``). Avoid
single-character variable names, unless inside a :term:`comprehension <list
comprehension>` or :ref:`generator <tut-generators>`.


We (mostly) follow NumPy style for docstrings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In most cases you can look at existing MNE-Python docstrings to figure out how
yours should be formatted. If you can't find a relevant example, consult the
`Numpy docstring style guidelines`_ for examples of more complicated formatting
such as embedding example code, citing references, or including rendered
mathematics.  Note that we diverge from the NumPy docstring standard in a few
ways:

1. We use a module called ``sphinxcontrib-bibtex`` to render citations. Search
   our source code (``git grep footcite`` and ``git grep footbibliography``) to
   see examples of how to add in-text citations and formatted references to
   your docstrings, examples, or tutorials. The structured bibliographic data
   lives in :file:`doc/references.bib`; please follow the existing key scheme
   when adding new references (e.g., ``Singleauthor2019``,
   ``AuthoroneAuthortwo2020``, ``FirstauthorEtAl2021a``,
   ``FirstauthorEtAl2021b``).
2. We don't explicitly say "optional" for optional keyword parameters (because
   it's clear from the function or method signature which parameters have
   default values).
3. For parameters that may take multiple types, we use pipe characters instead
   of the word "or", like this: ``param_name : str | None``.
4. We don't include a ``Raises`` or ``Warns`` section describing
   errors/warnings that might occur.


Private function/method docstrings may be brief for simple functions/methods,
but complete docstrings are appropriate when private functions/methods are
relatively complex. To run some basic tests on documentation, you can use::

    $ pytest mne/tests/test_docstring_parameters.py
    $ make ruff


Cross-reference everywhere
~~~~~~~~~~~~~~~~~~~~~~~~~~

Both the docstrings and dedicated documentation pages (tutorials, how-to
examples, discussions, and glossary) should include cross-references to any
mentioned module, class, function, method, attribute, or documentation page.
There are sphinx roles for all of these (``:mod:``, ``:class:``,
``:func:``, ``:meth:``, ``:attr:``, ``:doc:``) as well as a generic
cross-reference directive (``:ref:``) for linking to specific sections of a
documentation page.

.. warning::

    Some API elements have multiple exposure points (for example,
    ``mne.set_config`` and ``mne.utils.set_config``). For cross-references to
    work, they must match an entry in :file:`doc/python_reference.rst` (thus
    ``:func:`mne.set_config``` will work but ``:func:`mne.utils.set_config```
    will not).

MNE-Python also uses Intersphinx_, so you can (and should)
cross-reference to Python built-in classes and functions as well as API
elements in :mod:`NumPy <numpy>`, :mod:`SciPy <scipy>`, etc. See the Sphinx
configuration file (:file:`doc/conf.py`) for the list of Intersphinx projects
we link to. Their inventories can be examined using a tool like `sphobjinv`_ or
dumped to file with commands like::

    $ python -m sphinx.ext.intersphinx https://docs.python.org/3/objects.inv > python.txt

Note that anything surrounded by single backticks that is *not* preceded by one
of the API roles (``:class:``, ``:func:``, etc) will be assumed to be
in the MNE-Python namespace. This can save some typing especially in
tutorials; instead of ``see :func:`mne.io.Raw.plot_psd` for details`` you can
instead type ``see `mne.io.Raw.plot_psd` for details``.


Other style guidance
~~~~~~~~~~~~~~~~~~~~

- Use single quotes whenever possible.

- Prefer :ref:`generators <tut-generators>` or
  :term:`comprehensions <list comprehension>` over :func:`filter`, :func:`map`
  and other functional idioms.

- Use explicit functional constructors for builtin containers to improve
  readability (e.g., :ref:`list() <func-list>`, :ref:`dict() <func-dict>`,
  :ref:`set() <func-set>`).

- Avoid nested functions or class methods if possible — use private functions
  instead.

- Avoid ``*args`` and ``**kwargs`` in function/method signatures.

Code organization
-----------------

Importing
~~~~~~~~~

Import modules in this order, preferably alphabetized within each subsection:

1. Python built-in (``copy``, ``functools``, ``os``, etc.)
2. NumPy (``numpy as np``) and, in test files, pytest (``pytest``)
3. MNE-Python imports (e.g., ``from .pick import pick_types``)

When importing from other parts of MNE-Python, use relative imports in the main
codebase and absolute imports in tests, tutorials, and how-to examples. Imports
for ``matplotlib``, ``scipy``, and optional modules (``sklearn``, ``pandas``,
etc.) should be nested (i.e., within a function or method, not at the top of a
file). This helps reduce import time and limit hard requirements for using MNE.


Return types
~~~~~~~~~~~~

Methods should modify inplace and return ``self``, functions should return
copies (where applicable). Docstrings should always give an informative name
for the return value, even if the function or method's return value is never
stored under that name in the code.


Visualization
~~~~~~~~~~~~~

Visualization capabilities should be made available in both function and method
forms. Add public visualization functions to the :mod:`mne.viz` submodule, and
call those functions from the corresponding object methods. For example, the
method :meth:`mne.Epochs.plot` internally calls the function
:func:`mne.viz.plot_epochs`.

All visualization functions must accept a boolean ``show`` parameter and
typically return a :class:`matplotlib.figure.Figure` (or a list of
:class:`~matplotlib.figure.Figure` objects). 3D visualization functions return
a :class:`mne.viz.Figure3D`, :class:`mne.viz.Brain`, or other return type
as appropriate.

Visualization functions should default to the colormap ``RdBu_r`` for signed
data with a meaningful middle (zero-point) and ``Reds`` otherwise. This applies
to both visualization functions and tutorials/examples.

.. include:: _links.inc
