:title: stdlib-timeit
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The timeit module of
====================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, March 17th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/timeit/

.. image:: images/creative-commons.png

----

A series
========

on the standard library
~~~~~~~~~~~~~~~~~~~~~~~

- You should really check out `Doug Hellman's Python Module of the Week
  <http://pymotw.com/2/>`_.   It was started as a way to build the habit of
  writing something on a regular basis. The focus of the series is building a
  set of example code for the modules in the Python standard library.

- I'd like to do a running series of **talks** covering modules from the
  standard library that you may or may not know.  Doug already did this in blog
  form.  I'd like to update and expand it where I can.

----

:data-x: r0
:data-y: r900


Sometimes the stdlib
====================
is mind-blowing
~~~~~~~~~~~~~~~

- But not this time.

- It's more like a less clumsy way to do what you could otherwise do.

----

Like this guy
=============
less clumsy
~~~~~~~~~~~

.. image:: http://i.imgur.com/h0s0F.gif
   :width: 700px

----

:data-x: r1600
:data-y: 0

Not as bad as that guy
======================
but still a little clumsy
~~~~~~~~~~~~~~~~~~~~~~~~~

Here's what you would normally do without the ``timeit`` module.

.. code:: python

    import time

    start = time.time()
    some_function()  # Your code goes here
    end = time.time()
    delta = end - start
    print some_function, "took", delta, "seconds to complete"

----

:data-x: r1600
:data-y: 0

With timeit
===========

The ``timeit`` module provides three different ways to time the execution of stuff.

- The ``timeit.Timer`` class.
- The ``timeit.timeit(...)`` convenience function.
- Command line invocation of the module itself.

----

:data-x: r0
:data-y: r900

With the Timer class
====================

.. code:: python

    >>> import timeit
    >>> a_timer_object = timeit.Timer('print x * 2', setup='x = 4;print "setup"')
    >>> result = a_timer_object.timeit(2)
    setup
    8
    8
    >>> print result
    0.000472068786621

Note:

- ``timeit.Timer(...)`` can take two arguments there.  The first one is timed,
  the second is executed just to set things up for the first statement.
- ``a_timer_object.timeit(...)`` can take an optional integer, indicating how
  many times the statement should be executed (over which the time is
  averaged).

----

Measuring something more interesting
====================================
first, some setup
~~~~~~~~~~~~~~~~~

Let's set up some pre-amble for a more interesting comparison:

.. code:: python

    import timeit
    import sys

    # A few constants
    range_size=1000
    count=1000
    setup_statement="l = [ (str(x), x) for x in range(%d) ]; d = {}" % range_size

.. code:: python

    def show_results(result):
        "Print results in terms of microseconds per pass and per item."
        global count, range_size
        per_pass = 1000000 * (result / count)
        print '%.2f usec/pass' % per_pass,
        per_item = per_pass / range_size
        print '%.2f usec/item' % per_item

----

Things to measure
=================

Here are five different ways of populating a dict.

.. code:: python

    candidates = [
        ('__setitem__', """for s, i in l:
                               d[s] = i
                        """),

        ('setdefault',  """for s, i in l:
                               d.setdefault(s, i)
                        """),

        ('has_key',     """for s, i in l:
                               if not d.has_key(s):
                                   d[s] = i
                        """),

        ('KeyError',    """for s, i in l:
                               try:
                                   existing = d[s]
                               except KeyError:
                                   d[s] = i
                        """),

        ('"not in"',    """for s, i in l:
                               if s not in d:
                                   d[s] = i
                        """),
    ]

----

Running all that
================
for science
~~~~~~~~~~~

.. code:: python

    print "%d items" % range_size
    print "%d iterations" % count
    print

    for name, code in candidates:
        print "%s:\t" % name,
        sys.stdout.flush()
        t = timeit.Timer(code, setup_statement)
        show_results(t.timeit(number=count))

----

Results
=======
are surprising
~~~~~~~~~~~~~~

.. code::

    1000 items
    1000 iterations

    __setitem__:    76.43 usec/pass 0.08 usec/item
    setdefault:    167.96 usec/pass 0.17 usec/item
    has_key:       111.50 usec/pass 0.11 usec/item
    KeyError:       84.92 usec/pass 0.08 usec/item
    "not in":       62.72 usec/pass 0.06 usec/item

----

But if you vary
===============
those parameters
~~~~~~~~~~~~~~~~

.. code::

    10000 items
    10000 iterations

    __setitem__:    550.07 usec/pass 0.06 usec/item
    setdefault:    1146.03 usec/pass 0.11 usec/item
    has_key:       1115.52 usec/pass 0.11 usec/item
    KeyError:       955.21 usec/pass 0.10 usec/item
    "not in":       661.69 usec/pass 0.07 usec/item

Anyways...

----

:data-x: r1600
:data-y: 0

Lastly
======

You can run this thing from the command line like this:

.. code:: bash

    $ python -m timeit -s "d={}" "for i in range(1000):" "  d[str(i)] = i"
    1000 loops, best of 3: 289 usec per loop

----

:data-x: r1600
:data-y: 0

This has been
=============

The timeit Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, March 17th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/timeit/

.. image:: images/creative-commons.png
