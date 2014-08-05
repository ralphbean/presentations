:title: rube-flock14
:css: css/style-fedora.css
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

Any one of us can do this -- and *should* do this when we update things
in staging.

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

An example test
===============

.. code:: python

    class TestWiki(rube.fedora.FedoraRubeTest):
        base = "https://stg.fedoraproject.org/wiki"
        logout_url = "https://stg.fedoraproject.org/w/index.php" + \
            "?title=Special:UserLogout"
        title = "FedoraProject"

        @rube.core.tolerant()
        @rube.core.expects_zmqmsg('stg.wiki.article.edit')
        def test_login_and_edit(self):
            self.driver.get("https://stg.fedoraproject.org/wiki/Rube_Test_Page")
            elem = self.driver.find_element_by_css_selector("#ca-edit > a")
            elem.click()

            elem = self.driver.find_element_by_id("wpTextbox1")
            elem.send_keys(Keys.PAGE_DOWN)
            tag = str(uuid.uuid4())
            s = "Test comment from Rube\n%s" % tag
            elem.send_keys(s)
            elem = self.driver.find_element_by_css_selector("#wpSave")
            elem.submit()

            self.wait_for(tag)

----

:data-x: r0
:data-y: r900

rube.core
=========
has some decorators
~~~~~~~~~~~~~~~~~~~

``@rube.core.tolerant(n=3)`` tries to run your test.  If it succeeds, it does
nothing more.  If your test fails, it tries again and again (up to ``n``
times, by default 3 times).  If it fails all ``n`` times, the failure is
reported in the test.  This is useful if your connection is flaky, or you
know that one app is sometimes on the fritz.

----

``@rube.core.skip_logout()`` perhaps somewhat obviously will add your test to
a hidden ``_no_teardown`` list.  The ``tearDown`` method will skip it when
the time comes.

----

``@rube.core.expects_zmqmsg(topic, timeout=20000)`` will cause rube to start
up a background thread with a ``zmq.SUB`` socket.  It will connect to
whatever endpoint you have listed in ``setup.cfg`` like this::

  [zeromq]
  tcp://stg.fedoraproject.org:9940

If a message does not arrive with the specified multipart prefix before the
timeout has elapsed, then that test will fail.  In Fedora Infrastructure, we
use this to ensure that actions triggered on webapps by rube cause `fedmsg
<http://fedmsg.com>`_ messages to be published on our staging gateway.

----

``@rube.core.ensures_after(callable)`` will invoke ``callable`` after your
test has run, giving it a chance to raise an exception.

The common use case is to define a callable that executes a shell
command.  For instance, you could have a selenium test that goes to an
account system and applies for a dummy user's membership in a group.  After
that test has run, your callable could use paramiko to ssh to a machine and
ensure that that user now has shell access (or something).

----

``@rube.core.collect_har`` will collect HAR performance data on your
websites.  You have to do a little extra work (including setting up
browsermob-proxy) in order to get this work.  See below.

----

:data-x: r1600
:data-y: 0

Two things
==========
in conclusion
~~~~~~~~~~~~~

----

:data-x: r0
:data-y: r900

As we build and deploy new systems, we ought to add and update rube
tests.

We've already done this in place for new things like pkgdb2 and even
bodhi2 (the test instance)!

----

It would be nice to look into running this either nightly or in
response to certain events in headless mode on a server somewhere.

We could have this publish fedmsg messages.  Keep a dashboard.

Track performance enhancements or degradations over time with HAR files.

----

Fin.
