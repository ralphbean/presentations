:title: fedmsg-flock13
:css: css/style-fedmsg.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z


----

fedmsg
======
The Fedora Infrastructure Message Bus
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

FLOCK13, Charleston SC, August 9th, 2013

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

Go sit in ``#fedora-fedmsg`` on ``irc.freenode.net``.

http://threebean.org/presentations/fedmsg-flock13/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

----

fedmsg
======
what it is
~~~~~~~~~~

----

:data-x: r0
:data-y: r900

The `Fedora Infrastructure Message Bus <http://fedmsg.com>`_ is a
python package and API used around Fedora Infrastructure to send and
receive messages to and from applications.

We want to hook all the services in Fedora Infrastructure up to send
messages to one another over a message bus instead of communicating
with each other in the heterogeneous, “Rube-Goldberg” ways they do now.

----

.. image:: https://upload.wikimedia.org/wikipedia/commons/3/31/Rube_goldberg_machine.jpg
   :height: 800px

----

:data-x: r1600
:data-y: 0
:data-scale: 1

It uses *X.509 certificates* to sign messages.  It's meant to be deployed
in an open infrastructure.  Anyone can read.  Anyone can write.  Only
some messages are trusted.

It is built on top of `ØMQ <http://zeromq.org>`_.  There is no central
broker and as far as we can tell, no single point of failure.

Adding it to applications is *lightweight* -- sending is "fire-and-forget".

----

:data-x: r0
:data-y: r900

.. image:: https://raw.github.com/fedora-infra/fedmsg/f0e6a12a2737fae096c4f3ccc8ab7d8386459c50/doc/_static/topology.png
   :height: 625px

----

:data-x: r1600
:data-y: 0

It is *publicly subscribable* -- hit up ``tcp://hub.fedoraproject.org:9940``
with a ``zmq.SUB`` socket.

It has Fedora in the name, but `Debian Infrastructure is picking it up
<http://lists.debian.org/debian-qa/2013/04/msg00010.html>`_
this summer.  They're `making progress
<http://blog.olasd.eu/2013/07/bootstrapping-fedmsg-for-debian/>`_ too.  We now
need to change the name to mean the *FEDerated Message Bus* instead.

----

:data-x: r1600
:data-y: 0

fedmsg
======
how to do it
~~~~~~~~~~~~

----

:data-x: r0
:data-y: r900

first
=====
you have to get it
~~~~~~~~~~~~~~~~~~

.. code:: bash

    $ sudo yum -y install fedmsg

----

sending messages
================
from the shell
~~~~~~~~~~~~~~

.. code:: bash

    $ echo "Hello World." | fedmsg-logger --modname=git --topic=repo.update

    $ echo '{"a": 1}' | fedmsg-logger --json-input

    $ fedmsg-logger --message="This is a message."

    $ fedmsg-logger --message='{"a": 1}' --json-input

----

sending messages
================
from python
~~~~~~~~~~~

.. code:: python

    import fedmsg

    fedmsg.publish(
        topic='testing',
        msg={
            'test': 'Hello World',
            'foo': jsonifiable_objects,
            'bar': a_sqlalchemy_object,
        }
    )

----

:data-x: r1600
:data-y: 900

consuming messages
==================
at the command line
~~~~~~~~~~~~~~~~~~~

.. code:: bash

    $ fedmsg-tail --really-pretty

.. code:: python

    {
        "i": 1,
        "timestamp": 1344344053.2337201,
        "topic": "org.fedoraproject.prod.bodhi.update.comment",
        "msg": {
            "comment": {
                "update_title": "nethack4-4.0.0-1.fc20",
                "group": None,
                "author": "ralph",
                "text": "I'm so pumped to pwn those minotaurs!",
                "karma": 1,
                "anonymous": False,
                "timestamp": 1344344050.0
            }
        }
    }

----

:data-x: r0
:data-y: r900

consuming messages
==================
from python
~~~~~~~~~~~

.. code:: python

    import fedmsg

    for name, endpoint, topic, msg in fedmsg.tail_messages():
        print topic, msg

----

:data-x: r0
:data-y: r900
:data-scale: 0.5

consuming messages
==================
at the command line... an aside
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are lots of fun options to ``fedmsg-tail``.

*(Aside: there is a plugin system to provide domain-specific metadata about
messages.  Debian is working on their own.  Install the Fedora one!)*

.. code:: bash

   $ sudo yum -y install python-fedmsg-meta-fedora-infrastructure

With that, you can use the more fantastic options:

.. code:: bash

   $ fedmsg-tail --terse

.. code:: text

    buildsys.build.state.change -- ausil's tncfhh-0.8.3-14.fc20 completed
    http://koji.fedoraproject.org/koji/buildinfo?buildID=439734
    trac.ticket.update -- kevin closed a ticket on the Fedora Infrastructure trac instance as 'fixed'
    https://fedorahosted.org/fedora-infrastructure/ticket/3904
    bodhi.update.request.testing -- mmckinst submitted nawk-20121220-1.fc18 to testing
    https://admin.fedoraproject.org/updates/nawk-20121220-1.fc18
    wiki.article.edit -- Hguemar made a wiki edit to "Flock:Rideshare"
    https://fedoraproject.org/w/index.php?title=Flock:Rideshare&diff=prev&oldid=347430

----

:data-scale: 0.25

consuming messages
==================
like you're living in the future
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can run:

.. code:: bash

    $ fedmsg-tail --gource | gource --log-format custom -

Which makes something `like this
<http://threebean.org/so-i-turned-the-fedmsg-data-into-a-git-log-and.webm>`_.

----

:data-x: r1600
:data-y: 0
:data-scale: 1

topics
======
what messages?
~~~~~~~~~~~~~~

----

:data-x: r0
:data-y: r900

topics
======

Full list at http://fedmsg.com/en/latest/topics/, including:

- askbot.post.edit
- bodhi.update.comment
- bodhi.update.request.testing
- buildsys.build.state.change
- fas.group.member.sponsor
- fas.role.update
- git.receive
- meetbot.meeting.start
- pkgdb.acl.update
- planet.post.new
- wiki.article.edit

----

:data-x: r1600
:data-y: 0

things
======
that use fedmsg
~~~~~~~~~~~~~~~

- dwa's `koji stalk <http://dwa.fedorapeople.org/wip/koji-stalk.py>`_
- datanommer and `datagrepper! <https://apps.fedoraproject.org/datagrepper>`_
- fedora `badges <https://apps.fedoraproject.org/badges>`_
- pingou's `fedora-news <http://ambre.pingoured.fr/fedora-news/>`_
- pingou's `this-week-in-fedora <http://ambre.pingoured.fr/thisweekinfedora/>`_
- pingou's `owner changes report tool
  <https://lists.fedoraproject.org/pipermail/infrastructure/2013-June/013070.html>`_
- herlo's `FAS2Trac fama updater (ftl) <https://git.fedorahosted.org/cgit/ftl.git>`_
- p3ck's `fedmsg-download <https://github.com/p3ck/fedmsg-download/>`_
- lmacken's `fedmsg-notify <http://lewk.org/blog/fedmsg-notify>`_
- fedora `bus monitor <https://apps.fedoraproject.org/busmon>`_
- gource, `of course <http://threebean.org/blog/fedmsg-live-gource/>`_

----

:data-x: r1600
:data-y: 0

future
======
stuff
~~~~~

----

:data-x: r0
:data-y: r900

fedora
======
mobile
~~~~~~

- See Ricky Elrod's `landing page <http://fedoramobile.elrod.me/>`_

----

:data-x: r0
:data-y: r900

future
======
stuff
~~~~~

**fedmsg-notifications.** -- *Problem:* all of our
applications carry their own email code.  With that comes further baggage
and maintenance.

With fedmsg notifications for interesting infrastructure events, we can
put all that code in one place where it can be more easily maintained.

Benefit to the end-user:  manage notification preferences in one place
instead of per-app.

What about notifications to different *contexts*?  Email?
IRC privmsg?  Android?  RSS?

`A repo has been created
<https://github.com/fedora-infra/fedmsg-notifications>`_
for this but contains nothing more than notes at this point.

----

:data-x: r0
:data-y: r900

future
======
stuff
~~~~~

**Mirror pushing.** -- *Problem:* We have over 200 mirrors that help serve
Fedora releases.  You can read more about them `here
<https://fedoraproject.org/wiki/Infrastructure/Mirroring>`_.
As it stands they all run ``rsync`` on some interval to poll for new content.

There was some discussion of pushing the data years ago, but understandably,
mirror admins are reluctant to allow someone access to push content onto their
machines.  With a fedmsg solution, we only push a notification; the pulling
is still within the admin's control.

There is a `pull request <https://github.com/fedora-infra/fedmsg/pull/158>`_
waiting for review that will add a ``fedmsg-trigger`` command to fedmsg core.
We can use that to kick off rsync jobs when messages matching certain criteria
are received.

----

:data-x: r0
:data-y: r900

future
======
stuff
~~~~~

- Debian deployment!  See the latest `post from @olasd <http://blog.olasd.eu/2013/07/bootstrapping-fedmsg-for-debian/>`_.
- To listen to debian messages on your box, add this
  to ``/etc/fedmsg.d/endpoints.py``:

.. code:: python

    # You can get messages from here too!
    "debian-infrastructure": [
        "tcp://fedmsg.olasd.eu:9940",
    ],

- new features

  - persistance
  - gpg signatures
  - dns discovery

----

:data-x: r1600
:data-y: r0

*fín*
-----

Get the source:

- http://fedmsg.com
- http://github.com/fedora-infra/fedmsg

Presented by:

- Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

http://threebean.org/presentations/fedmsg-flock13/

Development discussed in ``#fedora-apps``.

Join ``#fedora-fedmsg`` for the firehose.

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png
