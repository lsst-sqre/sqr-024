..
  Technote content.

  See https://developer.lsst.io/restructuredtext/style.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.

.. note::

   **This technote is not yet published.**

   This technote outlines the steps required to enable flake8 testing and Travis CI for existing DM repos for which this support has not been enabled.

.. Add content here.
.. Do not include the document title (it's automatically added from metadata.yaml).

Introduction
============

In this technote we describe how to enable flake8 testing and Travis CI for existing DM repos for which this support has not been enabled. The steps are outlined here in essentially a "recipe" form, and assume that one is working on the development version of the LSST Science Platform (lsp-dev).

Steps
=====

1. Log onto `lsp-dev`_.

.. _lsp-dev: https://lsst-lspdev.ncsa.illinois.edu/

2. On lsp-dev, open a terminal.

3. Set up the LSST stack by typing the following in the terminal: :command:`source /opt/lsst/software/stack/loadLSST.bash ; setup lsst_distrib`

4. Make a place to put the repository (for example, :file:`travisifying/`).

5. Change directories to the folder you just created (:command:`cd travisifying/` in this example), and clone the repository that you wish to work on. For example, to clone this technote's repo, type :command:`git clone https://github.com/lsst-sqre/sqr-024.git`.

6. Change directories into the top level of the cloned repository (e.g., :command:`cd sqr-024`).

7. Create a Jira ticket for this task (for our example, we'll call it DM-99999). We will use that ticket for a new git branch.

8. Create a branch in this repo: :command:`git checkout -b tickets/DM-99999` (replace `99999` with your ticket's number).

9. Set up the repository by typing :command:`setup -k -r .`

10. The ``flake8`` testing requires a file called :file:`setup.cfg` to be present in the top level of the repo. Put a template version of the ``setup.cfg`` in place by typing: :command:`curl -O https://raw.githubusercontent.com/lsst/templates/master/project_templates/stack_package/%7B%7Bcookiecutter.package_name%7D%7D/setup.cfg`.

    Note: As of 10/25/2018, if ``W504`` isn't included in the lists of tests to *ignore* in :file:`setup.cfg`, then you should add it.

11. Is there a :file:`tests/` directory in the repo? If so, are there any Python tests in that directory? If either of these is not true, then create them. An example test that simply imports a module and then confirms that it imported is located `here: <https://github.com/lsst/ctrl_pool/blob/master/tests/test_import.py>`_. You can copy this one and edit as needed. Note that some repos only contain configs. For these, you may want to simply create a dummy test that asserts some simple math.

12. If there is no :file:`Sconscript` file in :file:`tests/`, then add one by doing the following: :command:`cd tests ; curl -O https://raw.githubusercontent.com/lsst/templates/master/project_templates/stack_package/%7B%7Bcookiecutter.package_name%7D%7D/tests/SConscript ; cd ..`

13. Make sure this :file:`SConscript` file contains a line that reads: ``scripts.BasicSConscript.tests(pyList=[])`` (scons needs to be told that it is allowed to discover files, and not just run flake8 on files in the :file:`tests/` directory).

14. Run the flake8 linting tests by typing :command:`scons` at the command line.

15. Go through the list of linting errors (if any) and fix them, rerunning :command:`scons` until there are no errors.

16. Add a :file:`.travis.yaml` file in the top level of the repository: :command:`curl -O https://raw.githubusercontent.com/lsst/templates/master/project_templates/stack_package/%7B%7Bcookiecutter.package_name%7D%7D/.travis.yml`

17. Commit your changes and push them to github.

18. Go to `https://travis-ci.org/lsst/<repo_name>` and click the Activate button. (Note: you need to have proper permissions to do this, or ask somebody who has permissions to do it for you.)

19. Make a commit and check (at the url given in step 18) that travis runs. (Note: sometimes it needs a nudge. Do a "git commit --amend" and "git push --force" to wake travis up...)

20. Require travis to run by going to ``https://github.com/lsst/<repo_name>`` and navigate to ``Settings/Branches``.  Make sure both of these options are enabled:
    * Require branches to be up to date before merging
    * Continuous Integration Travis/CI
    (Note: you need to have proper (admin) permissions to do this, or ask somebody who does to do it for you.)

21. Run the ticket branch through Jenkins: First, go to `<https://ci.lsst.codes/>`_. On this page, search for "stack-os-matrix", then click `Run`. Enter the ticket branch and repo names of your repo, then start the Jenkins run. You will be invited to the `#dmj-stack-os-matrix` Slack channel (if you are not already in it), where you will be notified when the job completes.

22. *If Jenkins successfully ran*, then push your changes and merge to master. (If not, then fix whatever is causing Jenkins to fail before merging.)

23. Change the Jira ticket status to "Done".


.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
