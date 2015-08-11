:title: fedmsg-flock15
:css: css/style-fedora.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z
 
----

Refinance your technical debt
=============================
with microservices
------------------

Presented at `Flock 2015 <https://fedoraproject.org/wiki/Flock_2015>`_ by `@ralphbean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/debt-services-flock15/

.. image:: images/fedmsg-flock14-img/creative-commons.png

----

Let's talk about
================
technical debt
--------------

Invest in infrastructure credit-default-swaps to hedge against
toxic CSS assets?

Avoid negative amortization by liquifying our deployment portfolio?

**No** -- We're talking about a series of bad decisions where we end up using more
resources to accomplish less.

----

code smells
===========
(qualitative evaluation)
------------------------

- duplicated code (fas caching, openid client login, ...)
- long methods and large classes (supybot-fedora, ...)
- contrived complexity (fedbadges, ...)
- half-implemented features (datagrepper, ...)
- no or poor documentation (releng scripts, ...)
- commented out code? or incorrect comments!
- no tests? or broken tests! (anitya, ...)
- parts that no one wants to touch (python-fedora, FAS?, ...)
- does hacking on it feel good or bad?

----

the alpha
=========

- not knowing how to say **NO** to features
- not drinking the unit test kool-aid.
- "I'll take the shortcut and clean up the mess tomorrow" (٭°̧̧̧ω°̧̧̧٭)
- when the priority is pushing code over good design and reusability

  - "I want to be seen as a good hacker!"

----

the omega
=========

- At the end of a dev cycle, we have one more fragile thing.

- Even worse, future dev cycles *and* deployments take longer and involve
  more fear.

- Can produce fatigue, cynicism and delusional self-efficacy
  (or, hubris, a la pingou's talk)

  - In the end, this is the real thing I'm concerned with avoiding.

----

What's a talk
=============
without numbers?
----------------

Can we quantify our revulsion to take responsibility for code?

----

Consider
========
lead time
---------

Ideally, measuring the time from when features are committed to
when they are released **in production**.

For us, we can only measure the time from when feature are
committed to when they are released as a git tag.

Here's a random subset of the repos on my dev box with the
**average** lead time calculated. There are **all kinds** of
caveats to this calculation, so take these results with a
tablespoon of salt.

----

- trac-fedmsg-plugin 1 day
- pagure 2 days
- mote 2 days
- copr 4 days
- fedmsg-atomic-composer 4 days
- pkgdb2 5 days
- fedora-releng-dash 5 days
- anitya 7 days
- fedmsg_meta_fedora_infrastructure 9 days
- fmn.rules 13 days
- mirrormanager2 13 days
- tahrir-api 14 days
- fmn.lib 15 days
- fmn.consumer 16 days
- the-new-hotness 18 days
- datanommer 19 days

----

- github2fedmsg 19 days
- fedocal 19 days
- fmn.web 20 days
- fedmsg 23 days
- tahrir 31 days
- blockerbugs 34 days
- bodhi 38 days
- fedora-tagger 38 days
- pyrasite 41 days
- resultsdb_api 41 days
- fas 43 days
- packagedb-cli 43 days
- bugzilla2fedmsg 45 days
- resultsdb_frontend 46 days
- datagrepper 51 days

----

- resultsdb 51 days
- nuancier 52 days <-- think about this one
- fedbadges 62 days
- python-fedora 67 days
- supybot-fedora 67 days
- koji 90 days
- sigul 94 days
- fedora-packages 115 days
- kitchen 181 days  (ZOMG)

----

What
====
do?
---

----

Cultural practices
==================

Moral high-horse stuff

- Don't let it happen in the first place
- "Always leave the codebase cleaner than when you found it"

Institutions

- Code review (we do this).
- No features without a test, without docs.
- I really like the idea of devoting a week every two months where we all do only
  technical debt fighting.  Or some other scheme: have a
  rotating person every week?

----

Architectural practices
=======================

Microservices?

----

Characteristics
===============
of microservices
----------------

(according to the microservices talking heads)

- **componentization via services**:
  Obviously, we modularize stuff all the time.
  Compare this with componentization via libraries.
- **organized around "business capabilities"**:
  In enterprise organizations, think "billing" versus "shipping" versus "payroll".
  We're more or less on the ball with this, except for some outliers.
  Think of **wikitcms**.
- **smart endpoints, dumb pipes**:
  Think fedmsg!  Contrast with ESB (egregious spaghetti box).
  Compare with The Internet
- **decentralized government**:
  The idea is that you can choose the right tool for the job.
  We do our own (healthy) version of this.  No mandate on platform, framework, etc..
- **decentralized data management**:
  This is one of Amazon's famous rules.
  We mostly have this.  Some of our services communicate through the same DB.
- **infrastructure automation (and application telemetry!)**:
  Necessary to keep track of all the new services. We're now dynamically
  creating fedmsg endpoints but we could do nagios monitoring, iptables
  rules, etc.
- **design for failure**:
  Think of Netflix's famous "Chaos Monkey".
  We're mixed in this regard.
  For instance, fedmsg fails gracefully but think where bodhi1 fails when
  fas gets loaded.  There are plenty of other examples.
- **evolutionary design**:
  This is a cop of from the Agile development handbook.
  We're mixed in this regard, but mostly on point.

----

How Big
=======
is a microservice?
------------------

- one responsibility? (how about payroll? that's big)
- small enough to fit in my head?
- small enough to rewrite in 2 weeks.

----

Cultural Patterns
=================
for deployment
--------------

- you built it, you run it
- google has a "hand back to dev" bit in their policy
- required production telemetry for every feature(!) (think, graphs of logins)

----

Case Studies
============

One one hand:

  - old pkgdb
  - FAS
  - bodhi

On the other:

  - koschei
  - FMN
  - old pkgdb + fedora-tagger + appdata

----

Pre-requisites
==============
(generally speaking)
--------------------

- rapid provisioning, we don't have, but its getting better
- monitoring (platform and application), we have, but it's not automatic
- automated tests, elementary
- rapid application deployment (playbooks/manual/upgrade/\*.yml), getting better
- devops culture (we have this)

----

A toolchain
===========
for continuous deployment?
--------------------------

git repos to jenkins to dgroc to copr to $SYSTEM to ansible to servers

- would require that we get jenkins up-to-snuff
- would require a policy change to allow copr on infra
- would require $SYSTEM to be written
- would require upgrade playbooks where we don't have them.
- would require an arithmetic amount of work to get all our services ready in git, jenkins and ansible

----

Is it worth it?
===============

- working on the pre-requisites will be good for us anyways.
- **if** we're to split up our services more, then we will have to (or burnout under deployment tasks)

----

Refinance your technical debt
=============================
with microservices
------------------

Presented at `Flock 2015 <https://fedoraproject.org/wiki/Flock_2015>`_ by `@ralphbean <http://threebean.org>`_.

Slides available at http://threebean.org/presentations/debt-services-flock15/

.. image:: images/fedmsg-flock14-img/creative-commons.png
