:title: gitflow
:css: css/style-gitflow.css
:data-transition-duration: 500
:skip-help: true


----

A successful Git branching model
--------------------------------

FOSSBox, Rochester, June 25th, 2013

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/gitflow/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

Images linked to from http://nvie.com

----

What is gitflow
---------------

- Two things:

  - Primarily -- a way to think about git branches and what to use them
    for.  Git is decentralized, and branches can quickly get out of hand.
    That can scare people away from using them.  But, technologically
    they're cheap as f*ck and they can help save you lots of pain.  The "git
    flow" model attempts to help save you from the pain of not using branches
    while mitigating the pain and confusing that too many or unorganized
    branches can bring.  It brings organization to the open-ended power of git
    (which is maybe more `wild <http://tartley.com/?p=1267>`_ than you might
    have `realized
    <https://twitter.com/pornelski/status/316190292443267073>`_).

  - A command line tool that makes using that model *easier* (maybe).  Sweetly,
    it installs itself as an extension to git.  Usage looks something like
    this:

    - ``git flow init``
    - ``git flow feature start crazy-idea``
    - ``git flow feature finish crazy-idea``
    - ``git flow release start 1.9.0alpha``
    - ``git flow release finish 1.9.0alpha``

- You can (and should) get all this information from `the horse's mouth
  <http://nvie.com/posts/a-successful-git-branching-model/>`_.  Homeboy
  ``nvie`` is a whole 'nuther kettle of fish.'  

- I maintain the `gitflow <https://apps.fedoraproject.org/packages/gitflow/>`_
  package for Fedora and EPEL.

----

It looks something like this
----------------------------

.. image:: http://nvie.com/img/2009/12/Screen-shot-2009-12-24-at-11.32.03.png
   :height: 600px

----

Decentralized, but centralized
------------------------------

.. image:: http://nvie.com/img/2010/01/centr-decentr.png
   :height: 600px

----

A tangent -- some fancies
-------------------------

- First, run ``$ wget http://threebean.org/.githelpers`` in your home directory.
- Next, edit ``~/.gitconfig`` and add the following to a *new git command*::

    [alias]
        l = "!source ~/.githelpers && pretty_git_log"

- Try it out by jumping into a project you already have and run ``$ git l``.

----

The main branches
-----------------

.. image:: http://nvie.com/img/2009/12/bm002.png
   :height: 600px

----

Setting up some git flow
------------------------

- Run ``$ sudo yum -y install gitflow``
- Create a new crap repo to try it out:

  - ``$ mkdir -p ~/scratch/silly-repo``
  - ``$ cd ~/scratch/silly-repo/``
  - ``$ git init``
  - ``$ echo "This is some content" > this-is-a-file.txt``
  - ``$ git add this-is-a-file.txt``
  - ``$ git commit -m 'This is my first commit.'``

- Ok.. that was git.  You know that.  Try ``$ git l`` to see the history.
- For some git flow, try this:

  - ``$ git flow init  # You only have to do this once``

- Try that ``$ git l`` again.  You have a "master" and "develop" branch now.

----

Feature branches
----------------

.. image:: http://nvie.com/img/2009/12/fb.png
   :height: 600px

----

Try some feature branches
-------------------------

- ``$ git flow feature start i-have-an-idea``
- Make some changes, commit.
- ``$ git flow feature start i-have-another-idea``
- Make some changes, commit.
- Check the output with ``$ git l``
- You can switch between branches with ``git checkout``.  Try:

  - ``$ git checkout develop``
  - ``$ echo "watwat" > some_file.txt``
  - ``$ git add some_file.txt``
  - ``$ git commit -m 'Another commit'``
  - ``$ git l --all``

- ``$ git flow feature finish i-have-an-idea``
- ``$ git flow feature publish i-have-another-idea``


----

Merging features
----------------

.. image:: http://nvie.com/img/2010/01/merge-without-ff.png
   :height: 600px

----

Merging features with GitHub
----------------------------

- ``$ git flow feature publish i-have-an-idea``
- Open a pull request for review.

----

Hotfix branches
---------------

.. image:: http://nvie.com/img/2010/01/hotfix-branches1.png
   :height: 600px

----

*fín*
-----

Presented by:

- Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/gitflow/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

Images linked to from http://nvie.com
