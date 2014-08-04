:title: stdlib-contextlib
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The contextlib module of
========================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, July 15th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/contextlib/

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

When you find yourself
======================
writing python
~~~~~~~~~~~~~~

- Its so easy to write **more** code to solve an abstraction problem, to handle
  a little pattern, or to smooth over some rough edge..

- On the other hand, there are a **ton** of third-party modules available out
  there on the `Python Package Index <https://pypi.python.org>`_.  Search, and
  you'll find solutions to problems you didn't even know you had.

- Often though, the solution to **our** problem was right there with us, all
  along.  Right there in the standard library (stdlib).

- If we study it and know it, then we can have...

----

Totally Awesome Skills
======================

.. image:: http://i.imgur.com/DysYwY8.gif
   :width: 600px

----

:data-x: r1600
:data-y: 0

First
=====
you need to know context managers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

----

:data-x: r0
:data-y: r900

.. code:: python

    with open('/var/tmp/threebean.txt', 'w') as f:
        f.write('contents go here')
    # file is automatically closed

----

.. code:: python

    class Context(object):

        def __init__(self):
            print '__init__()'

        def __enter__(self):
            print '__enter__()'
            return self

        def __exit__(self, exc_type, exc_val, exc_tb):
            print '__exit__()'

    with Context():
        print 'Doing work in the context'

.. code::

    __init__()
    __enter__()
    Doing work in the context
    __exit__()

----

.. code:: python

    class PowerCrystalImmaculate(object):
        def __init__(self, context):
            print 'PowerCrystal.__init__()'

        def dominate(self):
            print 'PowerCrystal.dominate()'

        def __del__(self):
            print 'PowerCrystal.__del__'


    class Summoning(object):
        def __init__(self):
            print 'Summoning.__init__()'

        def __enter__(self):
            print 'Summoning.__enter__()'
            return PowerCrystalImmaculate(self)

        def __exit__(self, exc_type, exc_val, exc_tb):
            print 'Summoning.__exit__()'

    with Summoning() as crystal:
        crystal.dominate()

.. code::

    Summoning.__init__()
    Summoning.__enter__()
    PowerCrystal.__init__()
    PowerCrystal.dominate()
    Summoning.__exit__()
    PowerCrystal.__del__

----

Kinda
=====
...nice?
~~~~~~~~

The context manager API is OK.

It is *really* fun to use context managers, but maybe less fun to implement
them.

You have magic methods: some ``__enter__`` and ``__exit__`` to keep
around, and ``__exit__`` has a messy signature.

----

:data-x: r1600
:data-y: 0

Enter
=====
the contextlib module
~~~~~~~~~~~~~~~~~~~~~

.. image:: http://i.imgur.com/Bt3qlPZ.gif
   :width: 700px

----

:data-x: r0
:data-y: r900

Primarily, the ``contextlib`` module lets you turn a **generator** function
into a context manager.  Consider this example:

.. code:: python

    import contextlib

    @contextlib.contextmanager()
    def make_context():
        print '+ entering'
        yield PowerCrystal()
        print '+ exiting'

    with make_context() as crystal:
        crystal.dominate()

.. code::

    + entering
    PowerCrystal.__init__()
    PowerCrystal.dominate()
    + exiting
    PowerCrystal.__del__

----

If using ``yield`` feels more awkward, exception handling probably feels more
natural than those sticky ``__exit__(....)`` arguments.

.. code:: python

    import contextlib

    @contextlib.contextmanager
    def make_context():
        print '+ entering'
        try:
            yield PowerCrystal()
        except IOError:
            print "Network failure."
            sys.exit(2)
        except OtherwordlyException:
            print "The crystal has overpowered this dimension."
            sys.exit(3)
        finally:
            print '+ exiting'

    with make_context() as crystal:
        #crystal.dominate()
        raise IOError('zomg')

.. code::

    + entering
    PowerCrystal.__init__()
    Network failure.
    + exiting
    PowerCrystal.__del__

----

The ``contextlib`` module also supports a nice ``contextlib.closing`` manager:

.. code:: python

    import contextlib
    import socket

    address = ('en.wikipedia.org', 80)

    with contextlib.closing(socket.create_connection(address)) as sock:
        sock.send('GET / http/1.1\n\n')

----

:data-x: r1600
:data-y: 0

Let's just take
===============
a look inside
~~~~~~~~~~~~~

.. image:: https://i.imgur.com/JHCjLB0.gif
   :width: 800px


----

:data-x: r0
:data-y: r900

HOWEVER
-------

There is only **one** reference to contextlib in other stdlib modules:
``_osx_support.py`` line 69.

Furthermore, there nothing *special* in the CPython code that is needed for the
``contextlib`` implementation.  It is just pure Python.

----

:data-x: r0
:data-y: r900


.. code:: python

    def contextmanager(func):
        """ @contextmanager decorator. """
        @wraps(func)
        def helper(*args, **kwds):
            return GeneratorContextManager(func(*args, **kwds))
        return helper


    class GeneratorContextManager(object):
        """ Helper for @contextmanager decorator. """

        def __init__(self, gen):
            self.gen = gen

        def __enter__(self):
            try:
                return self.gen.next()
            except StopIteration:
                raise RuntimeError("generator didn't yield")

----

.. code:: python

    def __exit__(self, type, value, traceback):
        if type is None:
            try:
                self.gen.next()
            except StopIteration:
                return
            else:
                raise RuntimeError("generator didn't stop")
        else:
            if value is None:
                # Need to force instantiation so we can reliably
                # tell if we get the same exception back
                value = type()
            try:
                self.gen.throw(type, value, traceback)
                raise RuntimeError("generator didn't stop after throw()")
            except StopIteration, exc:
                # Suppress the exception *unless* it's the same exception that
                # was passed to throw().  This prevents a StopIteration
                # raised inside the "with" statement from being suppressed
                return exc is not value
            except:
                # only re-raise if it's *not* the exception that was
                # passed to throw(), because __exit__() must not raise
                # an exception unless __exit__() itself failed.  But throw()
                # has to raise the exception to signal propagation, so this
                # fixes the impedance mismatch between the throw() protocol
                # and the __exit__() protocol.
                if sys.exc_info()[1] is not value:
                    raise

----

:data-x: r1600
:data-y: r0

This has been
=============

The contextlib Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, July 15th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/contextlib/

.. image:: images/creative-commons.png
