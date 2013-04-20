:title: fedmsg
:css: css/style-fedmsg.css
:data-transition-duration: 500
:skip-help: true


----

fedmsg - The Fedora Infrastructure Message Bus
----------------------------------------------

Barcamp Rochester, April 20th, 2013

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/fedmsg/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

----

What is fedmsg
--------------

- The `Fedora Infrastructure Message Bus <http://fedmsg.com>`_ is a
  python package and API used around Fedora Infrastructure to send and
  receive messages to and from applications.  We also use the term to
  refer to the installation we have running already.

- We want to hook all the services in Fedora Infrastructure up to send
  messages to one another over a message bus instead of communicating
  with each other in the heterogenous, “Rube-Goldberg” ways they do now.
  You know.. with cron jobs.

- It uses *X.509 certificates* to sign messages.  It's meant to be deployed
  in an open infrastructure.  Anyone can read.  Anyone can write.  Only
  some messages are trusted.

- It is built on top of `ØMQ <http://zeromq.org>`_.  There is no central
  broker and as far as we can tell, no single point of failure.

- It is *publicly subscribable* -- hit up ``tcp://hub.fedoraproject.org:9940``
  with a ``zmq.SUB`` socket.

- It has Fedora in the name, but `Debian Infrastructure is talking
  <http://lists.debian.org/debian-qa/2013/04/msg00010.html>`_
  about picking it up this summer.  The code isn't Fedora-specific, but
  we might need to change the name to mean the *FEDerated Message Bus*
  instead or something.

----

Applications, you say?  What applications?
------------------------------------------

`These applications <http://fedmsg.com/en/latest/status/>`_:

- askbot, bodhi, compose, comp 2nd arch, fas, koji, meetbot, pkgdb, planet,
  scm, pkgdb2branch, tagger, wiki

These ones are pretty much done -- they just need to be pushed live.

- coprs, elections, nagios

We have these ones still to do.

- autoqa, bugzilla, fedocal, fedorahosted, mailman, netapp, sigul

----

It looks something like this
----------------------------

.. image:: http://www.fedmsg.com/en/latest/_images/topology.png
   :height: 600px

----

Can I see what's happening?
---------------------------

Yes.

There's a sweet irc bot in channel at ``#fedora-fedmsg`` on the freenode
network.  There's an `identi.ca bot <http://identi.ca/fedmsgbot>`_.  There's
a `websocket driven bus monitor using d3 and html5
<http://apps.fedoraproject.org/busmon>`_.

You can::

    $ sudo yum install fedmsg
    $ fedmsg-tail --really-pretty

And for super crazy, you can::

    $ fedmsg-tail --gource | gource --log-format custom -

Which makes something `like this
<http://threebean.org/so-i-turned-the-fedmsg-data-into-a-git-log-and.webm>`_.

----

Testimonials
------------

`Jordan Sissel <http://www.semicomplete.com>`_ says: "Cool idea, gives new
meaning to open infrastructure."

----

Baller!  Can I hack it?
-----------------------

.. code-block:: python

    >>> # Yes!
    >>> import fedmsg

    >>> # Read in the config from /etc/fedmsg.d/
    >>> config = fedmsg.config.load_config()

    >>> for name, endpoint, topic, msg in fedmsg.tail_messages(**config):
    ...     print topic, msg  # or use fedmsg.encoding.pretty_dumps(msg)

----

*fín*
-----

Get the source:

- http://github.com/fedora-infra/fedmsg
- http://github.com/fedora-infra/fedmsg_meta_fedora_infrastructure
- http://github.com/fedora-infra/datanommer
- http://github.com/fedora-infra/datagrepper
- http://fedmsg.com

Presented by:

- Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/fedmsg/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png
