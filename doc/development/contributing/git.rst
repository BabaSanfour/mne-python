.. _contributing-git:

Git and GitHub
==============

.. highlight:: console

Configuring git
---------------

.. admonition:: Git GUI alternative
    :class: sidebar note

    `GitHub desktop`_ is a GUI alternative to command line git that some users
    appreciate; it is available for |windows| Windows and |apple| MacOS.

To get set up for contributing, make sure you have git installed on your local
computer:

- On Linux, the command ``sudo apt install git`` is usually sufficient; see the
  `official Linux instructions`_ for more options.

- On MacOS, download `the .dmg installer`_; Atlassian also provides `more
  detailed instructions and alternatives`_ such as using MacPorts or Homebrew.

- On Windows, download and install `git for Windows`_. With Git BASH it provides its own shell that
  includes many Linux-equivalent command line programs that are useful for development.

  *Windows 10 also offers the* `Windows subsystem for Linux`_ *that offers similar
  functionality to git BASH, but has not been widely tested by MNE-Python
  developers yet and may still pose problems with graphical output (e.g. building the documentation)*


Once git is installed, the only absolutely necessary configuration step is
identifying yourself and your contact info::

   $ git config --global user.name "Your Name"
   $ git config --global user.email you@yourdomain.example.com

Make sure that the same email address is associated with your GitHub account
and with your local git configuration. It is possible to associate multiple
emails with a GitHub account, so if you initially set them up with different
emails, you can add the local email to the GitHub account.

Sooner or later, git is going to ask you what text editor you want it to use
when writing commit messages, so you might as well configure that now too::

   $ git config --global core.editor emacs    # or vim, or nano, or subl, or...

There are many other ways to customize git's behavior; see `configuring git`_
for more information.

Forking the MNE-Python repository
---------------------------------

Once you have git installed and configured, and before creating your local copy
of the codebase, go to the `MNE-Python GitHub`_ page and create a `fork`_ into
your GitHub user account.

This will create a copy of the MNE-Python codebase inside your GitHub user
account (this is called "your fork"). Changes you make to MNE-Python will
eventually get "pushed" to your fork, and will be incorporated into the
official version of MNE-Python (often called the "upstream version") through a
"pull request". This process will be described in detail below; a summary
of how that structure is set up is given here:

.. graphviz:: ../../_static/diagrams/git_setup.dot
   :alt: Diagram of recommended git setup
   :align: left

.. _basic-git:

Basic git commands
------------------

Learning to work with git can take a long time, because it is a complex and
powerful tool for managing versions of files across multiple users, each of
whom have multiple copies of the codebase. The :doc:`setup` process uses a few
of the basic git commands that are useful to an MNE-Python developer:

- :samp:`git clone {<URL_OF_REMOTE_REPO>}` (make a local copy of a repository)

- :samp:`git remote add {<NICKNAME_OF_REMOTE>} {<URL_OF_REMOTE_REPO>}` (connect
  a local copy to an additional remote)

- ``git fetch --all`` (get the current state of connected remote repos)

Other commands that you will undoubtedly need relate to `branches`_. Branches
represent multiple copies of the codebase *within a local clone or remote
repo*. Branches are typically used to experiment with new features while still
keeping a clean, working copy of the original codebase that you can switch back
to at any time. The default branch of any repo is called ``main``, and
it is recommended that you reserve the ``main`` branch to be that clean copy
of the working ``upstream`` codebase. Therefore, if you want to add a new
feature, you should first synchronize your local ``main`` branch with the
``upstream`` repository, then create a new branch based off of ``main`` and
`check it out`_ so that any changes you make will exist on that new branch
(instead of on ``main``)::

    $ git checkout main            # switch to local main branch
    $ git fetch upstream             # get the current state of the remote upstream repo
    $ git merge upstream/main      # synchronize local main branch with remote upstream main branch
    $ git checkout -b new-feature-x  # create local branch "new-feature-x" and check it out

.. tip::
    :class: sidebar

    You can save some typing by using ``git pull upstream/main`` to replace
    the ``fetch`` and ``merge`` lines above.

Now that you're on a new branch, you can fix a bug or add a new feature, add a
test, update the documentation, etc. When you're done, it's time to organize
your changes into a series of `commits`_. Commits are like snapshots of the
repository — actually, more like a description of what has to change to get
from the most recent snapshot to the current snapshot.

Git knows that people often work on multiple changes in multiple files all at
once, but that ultimately they should separate those changes into sets of
related changes that are grouped together based on common goals (so that it's
easier for their colleagues to understand and review the changes). For example,
you might want to group all the code changes together in one commit, put new
unit tests in another commit, and changes to the documentation in a third
commit.  Git makes this possible with something called the `stage`_ (or
*staging area*). After you've made some changes to the codebase, you'll have
what git calls "unstaged changes", which will show up with the `status`_
command::

    $ git status    # see what state the local copy of the codebase is in

Those unstaged changes can be `added`_ to the stage one by one, by either
adding a whole file's worth of changes, or by adding only certain lines
interactively::

    $ git add mne/some_file.py      # add all the changes you made to this file
    $ git add mne/some_new_file.py  # add a completely new file in its entirety
    $ # enter interactive staging mode, to add only portions of a file:
    $ git add -p mne/viz/some_other_file.py

Once you've collected all the related changes together on the stage, the ``git
status`` command will now refer to them as "changes staged for commit". You can
commit them to the current branch with the `commit`_ command. If you just type
``git commit`` by itself, git will open the text editor you configured it to
use so that you can write a *commit message* — a short description of the
changes you've grouped together in this commit. You can bypass the text editor
by passing a commit message on the command line with the ``-m`` flag. For
example, if your first commit adds a new feature, your commit message might be::

    $ git commit -m 'ENH: adds feature X to the Epochs class'

Once you've made the commit, the stage is now empty, and you can repeat the
cycle, adding the unit tests and documentation changes::

    $ git add mne/tests/some_testing_file.py
    $ git commit -m 'add test of new feature X of the Epochs class'
    $ git add -p mne/some_file.py mne/viz/some_other_file.py
    $ git commit -m 'DOC: update Epochs and BaseEpochs docstrings'
    $ git add tutorials/new_tutorial_file.py
    $ git commit -m 'DOC: adds new tutorial about feature X'

When you're done, it's time to run the test suite to make sure your changes
haven't broken any existing functionality, and to make sure your new test
covers the lines of code you've added (see :ref:`run-tests` and
:ref:`build-docs`). Once everything looks good, it's time to push your
changes to your fork::

    $ # push local changes to remote branch origin/new-feature-x
    $ # (this will create the remote branch if it doesn't already exist)
    $ git push origin new-feature-x

Finally, go to the `MNE-Python GitHub`_ page, click on the pull requests tab,
click the "new pull request" button, and choose "compare across forks" to
select your new branch (``new-feature-x``) as the "head repository".  See the
GitHub help page on `creating a PR from a fork`_ for more information about
opening pull requests.

If any of the tests failed before you pushed your changes, try to fix them,
then add and commit the changes that fixed the tests, and push to your fork. If
you're stuck and can't figure out how to fix the tests, go ahead and push your
commits to your fork anyway and open a pull request (as described above), then
in the pull request you should describe how the tests are failing and ask for
advice about how to fix them.

To learn more about git, check out the `GitHub help`_ website, the `GitHub
skills`_ tutorial series, and the `pro git book`_.


.. _github-ssh:

Connecting to GitHub with SSH (optional)
----------------------------------------

One easy way to speed up development is to reduce the number of times you have
to type your password. SSH (secure shell) allows authentication with pre-shared
key pairs. The private half of your key pair is kept secret on your computer,
while the public half of your key pair is added to your GitHub account; when
you connect to GitHub from your computer, the local git client checks the
remote (public) key against your local (private) key, and grants access your
account only if the keys fit. GitHub has `several help pages`_ that guide you
through the process.

Once you have set up GitHub to use SSH authentication, you should change the
addresses of your MNE-Python GitHub remotes, from ``https://`` addresses to
``git@`` addresses, so that git knows to connect via SSH instead of HTTPS. For
example::

    $ git remote -v  # show existing remote addresses
    $ git remote set-url origin git@github.com:$GITHUB_USERNAME/mne-python.git
    $ git remote set-url upstream git@github.com:mne-tools/mne-python.git



.. include:: _links.inc
