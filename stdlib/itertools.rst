:title: stdlib-itertools
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The itertools module of
=======================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, November 18th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/itertools/

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


It's important
==============
really
~~~~~~

- I usually include some solicitation here for you to take the stdlib
  seriously.  Let's forego the details and just say that:

- If you master the standard library, then you'll be amazing.  Your friends
  will be amazed.


----

You'll be like
==============

.. image:: http://i.imgur.com/VQLGJOL.gif
   :width: 700px

----

:data-x: r1600
:data-y: 0

Overview
========
of the itertools module
~~~~~~~~~~~~~~~~~~~~~~~

- Merging and Splitting Iterators
- Converting Inputs
- Producing New Values
- Filtering
- Grouping

----

:data-x: r1600
:data-y: 0

Merging and Splitting Iterators (1/4)
=====================================

The ``chain(...)`` function takes several iterators and produces a new
iterator as if they were all in sequence.

.. code:: python

    import itertools

    for i in itertools.chain([1, 2, 3], ['a', 'b', 'c']):
        print i

.. code::

    1
    2
    3
    a
    b
    c

*Point of confusion:*  What is the difference between ``chain(a, b)`` and ``a + b``?

----

:data-x: r0
:data-y: r900

Merging and Splitting Iterators (2/4)
=====================================

The ``izip(...)`` function combines the elements of several iterators into an
iterator of tuples:

.. code:: python

    import itertools

    for i in itertools.izip([1, 2, 3], ['a', 'b', 'c']):
        print i

.. code::

    (1, 'a')
    (2, 'b')
    (3, 'c')

*Point of confusion:*  What is the difference between ``izip(a, b)`` and ``zip(a, b)``?

----

Merging and Splitting Iterators (3/4)
=====================================

The ``product(...)`` function returns an iterator that produces the Cartesian
product of the given iterables:

.. code:: python

    import itertools

    for i in itertools.product([1, 2, 3], ['a', 'b', 'c']):
        print i

.. code::

    (1, 'a')
    (1, 'b')
    (1, 'c')
    (2, 'a')
    (2, 'b')
    (2, 'c')
    (3, 'a')
    (3, 'b')
    (3, 'c')

This is similar to:

.. code:: python

    for i in [1, 2, 3]:
        for j in ['a', 'b', 'c']:
            print (i, j)

But using ``itertools.product`` can help *un-nest* or flatten your code.

----

Merging and Splitting Iterators (4/4)
=====================================

The ``tee(original, n=2)`` function is wild.  It returns a tuple of ``n``
independent iterators.

.. code:: python

    iter1, iter2 = itertools.tee(range(3), 2)
    for i in iter1:
        print i
    for i in iter2:
        print i

.. code::

    0
    1
    2
    0
    1
    2

It's kind of like the ``tee`` program from GNU coreutils which does the same
kind of thing but with \*nix pipes instead of python iterators.

What could you do with this?  Asynchronously feed plugins in parallel?

----

:data-x: r1600
:data-y: 0

Converting Inputs (1/2)
=======================

Take a look at these two functions.  First the builtin, then the ``itertools`` version:

- ``map(function, iterable1, iterable2, ...)``
- ``itertools.imap(function, iterable1, iterable2, ...)``

.. code:: python

    for i in map(lambda x, y: "%r %r" % (x, y), range(3), range(2, 6)):
        print i

.. code::

    0 2
    1 3
    2 4
    None 5

.. code:: python

    from itertools import imap

    for i in imap(lambda x, y: "%r %r" % (x, y), range(3), range(2, 6)):
        print i

.. code::

    0 2
    1 3
    2 4

----

:data-x: r0
:data-y: r900

Converting Inputs (2/2)
=======================

``starmap`` is similar to ``imap``, except that it only accepts one sequence of
tuples, but those tuples are applied to the function using the
argument-expansion ``*args`` syntax.

.. code:: python

    import itertools

    values = [(0, 5), (1, 6), (2, 7), (3, 8), (4, 9)]
    for i in itertools.starmap(lambda x, y: (x, y, x*y), values):
        print '%d * %d = %d' % i

.. code::

    0 * 5 = 0
    1 * 6 = 6
    2 * 7 = 14
    3 * 8 = 24
    4 * 9 = 36

----

:data-x: r1600
:data-y: 0

Producing New Values (1/1)
==========================

Check out ``count(start=0, step=1)``.  It produces a consecutive integers, forever.

.. code:: python

    import itertools

    for i in itertools.count(3):
        if i == 7:
            break
        print i

.. code::

    3
    4
    5
    6

----

:data-x: r0
:data-y: r900

Producing New Values (2/3)
==========================

Also neat is ``cycle(iterable)`` which produces an infinite cycle over the
originally given iterable.

.. code:: python

    from itertools import izip, count, cycle

    for number, letter in izip(count(1), cycle(['a', 'b', 'c'])):
        if number == 8:
            break
        print number, letter

.. code::

    1 a
    2 b
    3 c
    4 a
    5 b
    6 c
    7 a

----

Producing New Values (3/3)
==========================

And there's ``repeat(object [, times])`` which, obviously, repeats an object
perhaps forever, or perhaps for a given number of times:

.. code:: python

    import itertools

    for item in itertools.repeat('wat', 4):
        print item

.. code::

    wat
    wat
    wat
    wat


----

:data-x: r1600
:data-y: 0

Filtering Values (1/4)
======================

The first one here, ``dropwhile(callable, sequence)`` is really weird.
It yields items from the sequence *after a condition becomes false for the
first time*... which seems really niche to me.

.. code:: python

    from itertools import dropwhile

    should_drop = lambda x: x < 1

    for i in dropwhile(should_drop, [ -1, 0, 1, 2, 3, 4, 1, -2 ]):
        print 'Yielding:', i

.. code::

    Yielding: 1
    Yielding: 2
    Yielding: 3
    Yielding: 4
    Yielding: 1
    Yielding: -2

Notice the ``-2`` on the end...

----

:data-x: r0
:data-y: r900

Filtering Values (2/4)
======================

There is ``takewhile(callable, sequence)`` which does just the opposite of
``dropwhile``.

.. code:: python

    from itertools import takewhile

    should_take = lambda x: x < 1

    for i in takewhile(should_take, [ -1, 0, 1, 2, 3, 4, 1, -2 ]):
        print i

.. code::

    -1
    0

----

Filtering Values (3/4)
======================

``ifilter(callable, sequence)`` is probably more familiar -- it returns an
iterator that works like the built-in ``filter(...)`` does for lists, including
only items for which the test function returns true.

**Key point:** It is different from ``dropwhile(...)`` in that every item is
tested before it is returned.

.. code:: python

    from itertools import ifilter

    check = lambda x: x < 1

    for i in ifilter(check, [ -1, 0, 1, 2, 3, 4, 1, -2 ]):
        print i

.. code::

    -1
    0
    -2

----

Filtering Values (4/4)
======================

This one seems unnecessary.. but there's also ``ifilterfalse`` which works just
the opposite.

.. code:: python

    from itertools import ifilterfalse

    check = lambda x: x < 1

    for i in ifilterfalse(check, [ -1, 0, 1, 2, 3, 4, 1, -2 ]):
        print i

.. code::

    1
    2
    3
    4
    1

----

:data-x: r1600
:data-y: 0

Grouping Values (1/1)
=====================


Finding keys from a dictionary that have common values with ``groupby``:

.. code:: python

    from itertools import groupby
    from operator import itemgetter

    # Our initial dict
    d = dict(a=1, b=2, c=1, d=2, e=1, f=2, g=3)

    # Make list of tuples sorted by 'value'
    d_sorted = sorted(d.iteritems(), key=itemgetter(1))

    # Group together keys that share the same value(s)
    for k, g in groupby(d_sorted, key=itemgetter(1)):
        print k, map(itemgetter(0), g)

.. code::

    1 ['a', 'c', 'e']
    2 ['b', 'd', 'f']
    3 ['g']

----

:data-x: r1600
:data-y: 0

The Big Reveal
==============

.. code:: python

    import itertools
    print itertools.__file__

.. code::

    /usr/lib64/python2.7/lib-dynload/itertoolsmodule.so

Wait... what?

.. code::

    ~/❯ wc -l Python-2.7.8/Modules/itertoolsmodule.c
    4135 Python-2.7.8/Modules/itertoolsmodule.c

4000 lines of C code.  And.. it's not the only one:

.. code::

    ~/❯ ls Python-2.7.8/Modules/*.c | wc -l
    99

Anyways...

----

:data-x: r1600
:data-y: 0

This has been
=============

The itertools Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, November 18th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/itertools/

.. image:: images/creative-commons.png
