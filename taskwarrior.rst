:title: taskwarrior
:css: css/style-redhat.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

how to stop being a task worrier
================================
and become a taskwarrior
------------------------

Presented at the RED tech share by `threebean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/taskwarrior/

.. image:: images/fedmsg-flock14-img/creative-commons.png

----

Agenda
======

- Philosophy (GTD)
- Simple usage
- The ``~/.taskrc`` file
- Access on your mobile device
- Reporting
- Integrating bug trackers

----

Philosophy
==========
getting things done
-------------------

`GTD
<http://lifehacker.com/productivity-101-a-primer-to-the-getting-things-done-1551880955>`_
is a general producitity method that is about organizing your own tasks.  There
are a number of aspects to it, but in particular:

- Get your to-dos out of your head and into a system.
- Reivew your list and keep it current.
- Consult your list to guide your actions.
- This allows you to clear your mind.

In order for it to work, you have to trust your habit.  This takes time.

----

Syntax
======

.. image:: https://taskwarrior.org/docs/images/syntaxes.png
   :width: 1000px

----

Let's go play
=============
in a terminal
-------------

- Adding and manipulating tasks
- Projects and tags
- Due dates
- Waiting

----

Configuration
=============

Four values are worth setting in your ``~/.taskrc`` file (See ``$ man taskrc``
for lots more...)::

    # For reporting
    _forcecolor=yes

    # A default project
    default.project=unsorted

    # A default priority
    uda.priority.default=H

    # For morale
    nag=You ROCK!

----

Access
======
from a mobile device
--------------------

Check out https://inthe.am/

- It's AGPL, and works on your phone.
- Synchronize with ``task sync``
- Under the hood it uses ``taskd``; you could run your own.

----

Generating
==========
reports
-------

**tl;dr**:  Feel free to steal my `timesheet.sh <https://github.com/ralphbean/lightsaber/blob/develop/roles/task/client/files/bin/timesheet.sh>`_ script.

- Check out the ``burndown``, ``history``, and ``ghistory`` commands.
- Use in combination with `ansi2html <https://github.com/ralphbean/ansi2html>`_.

----

Integrating
===========
bug trackers
------------

See https://bugwarrior.readthedocs.io ::

	[general]
	targets = jira.redhat
	#targets = jira.redhat, bz.redhat
	shorten = True
	inline_links = False
	annotation_links = True
	log.level = INFO
	#log.file = /home/threebean/bugwarrior.log
	multiprocessing = True
	legacy_matching = False

	[jira.redhat]
	service = jira
	jira.base_uri = https://projects.engineering.redhat.com
	jira.username = rbean
	jira.password = @oracle:eval:pass mail/redhat/krb5
	jira.verify_ssl = True
	jira.import_labels_as_tags = True
	jira.import_sprints_as_tags = True
	jira.default_priority = H

	#[bz.redhat]
	#service = bugzilla
	#bugzilla.base_uri = bugzilla.redhat.com
	#bugzilla.username = rbean@redhat.com
	#bugzilla.password = @oracle:eval:pass sites/rhbz

----

some shortcuts
==============
that I just made up
-------------------

I've accumulated a handful of useful sciprts `here <https://github.com/ralphbean/lightsaber/tree/develop/roles/task/client/files/bin>`_.

- ``next``
- ``twait-tomorrow``
- ``tdone``
- ``tanno "some string"``

You might also enjoy these `notes from Kevin Fenzi
<https://www.scrye.com/wordpress/nirik/2016/03/30/taskwarrior-tips-and-notes/>`_
of the Fedora Infrastructure team.

----

that's taskwarrior
==================
have fun!
---------

Presented at the RED tech share by `threebean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/taskwarrior/

.. image:: images/fedmsg-flock14-img/creative-commons.png
