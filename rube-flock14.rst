:title: rube-flock14
:css: css/style-redhat.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

rube
====
Web tests of Fedora Infra's staging environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

FLOCK14, Prague CZ, August, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

http://threebean.org/presentations/rube-flock14

.. image:: images/fedmsg-flock14-img/creative-commons.png

----

We have
=======
a staging environment
~~~~~~~~~~~~~~~~~~~~~

and a production environment.  There are differences, but in theory they're the
same setup.  We have the staging environment expressly for the purpose of
testing changes, bugfixes, and enhancements before they go "*live*" to the
production environment.

----

:data-x: r0
:data-y: r900

We often
========
manage these successfully
~~~~~~~~~~~~~~~~~~~~~~~~~

with explicit updates to a particular services.  Say we have a new release of
`fedocal <https://apps.fedoraproject.org/calendar>`_ with a new feature.  We'll
install the update in staging and try that feature out by hand.  If it works,
then great!  We'll push right to production.

----

Sometimes,
==========
though
~~~~~~

there are unintended side-effects to that particular system.

Even more tricky, there are sometimes side-effects to **other services** that
integrate with the service being updated.

Sometimes we need to update an **underlying library** that can break dependant
services in unforeseen ways.

----

The purpose
===========
of rube
~~~~~~~

is to provide an automated **suite of functional tests** that try to provide
some degree of certainty that we didn't break the world.

In the operations-engineering lingo of the day: the purpose of rube is to
**reduce fear**.

----

The name
========
comes from
~~~~~~~~~~

“Rube-Goldberg” machines: devices that accomplish some
mundane task in wildly complicated ways that integrate many moving and
typically unrelated parts.

When you see a Rube-Goldberg machine in action, it is easy to imagine that a
small change to one piece would cause the whole contraption to malfunction.

----

The purpose
===========
of this workshop
~~~~~~~~~~~~~~~~

is to educate others on the infrastructure team about how rube works and how to
update it -- how to use it and how to add new tests.

----

:data-x: r1600
:data-y: 0

Running it
==========
is simple
~~~~~~~~~

.. code:: bash

    $ sudo yum install python-virtualenvwrapper gcc-g++

.. code:: bash

    $ git clone git://github.com/fedora-infra/rube.git
    $ cd rube

.. code:: bash

    $ mkvirtualenv rube
    $ ./setup.sh

.. code:: bash

    $ ./runtests.sh

----

:data-x: r1600
:data-y: 0

look at a simple test
talk about the decorators
talk about uncovered services maybe?
