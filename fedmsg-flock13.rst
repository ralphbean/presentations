:title: fedmsg-flock13
:css: css/style-fedmsg.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z


----

fedmsg - The Fedora Infrastructure Message Bus
----------------------------------------------

FLOCK13, Charleston SC, August 9th, 2013

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/fedmsg-flock13/

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

----

:data-x: r0
:data-y: r900

.. image:: https://raw.github.com/fedora-infra/fedmsg/167c2d418936cd3c5e3a30e5880e4ca05fd53f39/doc/_static/topology.png
   :height: 700px

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

applications
applications:
- koji shadow
- koji stalk
- datanommer
- datagrepper
- fedora-news
- badges
- mirror pusher
- fama updater
- busmon
- gource

----

:data-x: r1600
:data-y: 0

design decisions:
- decentralized
- x.509 certs
- fire and forget

----

:data-x: r1600
:data-y: 0

future stuff
- fedmsg-notifications
- fedora-mobile
- debian
- persistance
- gpg signatures
- dns discovery

----

Testimonials
------------

`Jordan Sissel <http://www.semicomplete.com>`_ says: "Cool idea, gives new
meaning to open infrastructure."

----

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
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/fedmsg-flock2013/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png
