:title: factory2-flock16
:css: css/style-fedora.css
:data-transition-duration: 700
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z
 
----

The Productization Pipeline
===========================
and the so-called "Factory 2.0"
-------------------------------

Presented at `DevConf 2017 <https://devconf.cz>`_ by `@ralphbean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/factory2-devconf17/

.. image:: images/fedmsg-flock14-img/creative-commons.png

----

The Eternal September
=====================

A fun tidbit of *Hacker Lore*:

  Also called the **September that never ended**, the **Eternal September** is
  *Usenet slang* for a period beginning in September 1993, the month that
  Internet service provider America Online began offering Usenet access to its
  many users, overwhelming the existing culture for online forums.

----

:data-x: r0
:data-y: r900

The Second Eternal September
============================
and the rise of github
----------------------

.. image:: images/factory2-flock16-img/packages.png
   :width: 700px

This is fine.

----

Consequent Changes
==================

To *application development and deployment*:

``pip freeze > requirements.txt  # ship it!``

To the *traditional linux distribution*:

- nixOS
- coreOS
- docker and friends

In my opinion, `Modularity <https://fedoraproject.org/wiki/Modularity>`_ is
basically our attempt to address this.

----

:data-x: r1600
:data-y: 0

Factory 2.0
===========
is not what you think it is
---------------------------

We had a problem from the outset.  Lots of *myths* started to propagate about "Factory 2.0".

- Factory 2.0 is not **a single web application**.
- Factory 2.0 is not **a rewrite of our entire pipeline**.
- Factory 2.0 is not **a silver bullet**.
- Factory 2.0 is not **a silver platter**.
- Factory 2.0 is not **just Modularity**.
- Factory 2.0 is not **going to be easy**.

----

:data-x: r1600
:data-y: 0

the six problem statements
==========================
(we made a list)
----------------

- **Repetitive human intervention** makes the pipeline slow.
- **Unnecessary serialization** makes the pipeline slow.
- The pipeline imposes a rigid and **inflexible cadence** on products.
- The pipeline **makes assumptions** about the content being shipped.
- The distro is defined by packages, not “features” (**Modularity**).
- There’s no easy way to **trace deps** from upstream to product.

You can read more in our `design documents <https://fedoraproject.org/wiki/User:Ralph/Drafts/Infrastructure/Factory2>`_.

----

overview of the existing pipeline
continuous integration
building modules
containers from modules
branching schism
rebuild automation

----

:data-x: r1600
:data-y: 0

Thank you
=========

Presented at `DevConf 2017 <https://devconf.cz>`_ by `@ralphbean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/factory2-devconf17/

Hop in ``#fedora-modularity`` to join the party (we share it with the modularity team).

.. image:: images/fedmsg-flock14-img/creative-commons.png
