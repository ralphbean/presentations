:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The inspect module of
=====================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, October 21st, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/inspect/

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

- I'm doing a running series of **talks** covering modules from the standard
  library that you may or may not know.  Doug already did this in blog form.
  I'd like to update and expand it where I can.

----

:data-x: r0
:data-y: r900

When you find yourself
======================
writing python
~~~~~~~~~~~~~~

- Its so easy to **write more** code to solve an abstraction problem, to handle
  a little pattern, or to smooth over some rough edge..

- On the other hand, there are **already a ton** of third-party modules available out
  there on the `Python Package Index <https://pypi.python.org>`_.  Search, and
  you'll find solutions to problems you didn't even know you had.

- Often though, the solution to our problem was **right there with us**, all
  along.  Right there in the standard library (stdlib).

- If we study it and know it, then we can have...

----

Totally Awesome Skills
======================

.. image:: http://sli.relay.im/k/S/X/kSXFbmRICoo-large.gif
   :width: 600px

----

:data-x: r1600
:data-y: 0

First
=====
Here are some example objects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

----

:data-x: r0
:data-y: r900

.. code:: python

    """ Sample file to serve as the basis for inspect examples. """

    def module_level_function(arg1, arg2='default', *args, **kwargs):
        """This function is declared in the module."""
        local_variable = arg1
        return

    class A(object):
        """The A class."""
        def __init__(self, name):
            self.name = name

        def get_name(self):
            "Returns the name of the instance."
            return self.name

    instance_of_a = A('sample_instance')

    class B(A):
        """This is the B class.
        It is derived from A.
        """

        # This method is not part of A.
        def do_something(self):
            """Does some work"""
            pass

        def get_name(self):
            "Overrides version from A"
            return 'B(' + self.name + ')'

----

:data-x: r1600
:data-y: 0

Take a look at the ``getmembers`` function.

.. code:: python

    import inspect

    import example

    for name, data in inspect.getmembers(example):
        if name == '__builtins__':
            continue
        print '%s :' % name, repr(data)


The code above would produce output that looks like this:

.. code::

    A : <class 'example.A'>
    B : <class 'example.B'>
    __doc__ : 'Sample file to serve as the basis for inspect examples.'
    __file__ : '/home/threebean/example.pyc'
    __name__ : 'example'
    instance_of_a : <example.A object at 0xbb810>
    module_level_function : <function module_level_function at 0xc8230>

You get similar results with ``dir(example)`` and ``example.__dict__.keys()``.

----

:data-x: r0
:data-y: r900

You can pass a second ``predicate`` argument to ``inspect.getmembers`` to limit
the kinds of members you want to be returned.

.. code:: python

    import inspect

    import example

    for name, data in inspect.getmembers(example, inspect.isclass):
        print '%s :' % name, repr(data)

.. code::

    A : <class 'example.A'>
    B : <class 'example.B'>

----


Looking at classes further...

.. code:: python

    import inspect
    from pprint import pprint

    import example

    pprint(inspect.getmembers(example.A))

.. code::

    [('__class__', <type 'type'>),
     ('__delattr__', <slot wrapper '__delattr__' of 'object' objects>),
     ('__dict__', <dictproxy object at 0xca090>),
     ('__doc__', 'The A class.'),
     ('__getattribute__', <slot wrapper '__getattribute__' of 'object' objects>),
     ('__hash__', <slot wrapper '__hash__' of 'object' objects>),
     ('__init__', <unbound method A.__init__>),
     ('__module__', 'example'),
     ('__new__', <built-in method __new__ of type object at 0x32ff38>),
     ('__reduce__', <method '__reduce__' of 'object' objects>),
     ('__reduce_ex__', <method '__reduce_ex__' of 'object' objects>),
     ('__repr__', <slot wrapper '__repr__' of 'object' objects>),
     ('__setattr__', <slot wrapper '__setattr__' of 'object' objects>),
     ('__str__', <slot wrapper '__str__' of 'object' objects>),
     ('__weakref__', <attribute '__weakref__' of 'A' objects>),
     ('get_name', <unbound method A.get_name>)]

----

And paring it down to only methods...

.. code:: python

    import inspect
    from pprint import pprint

    import example

    pprint(inspect.getmembers(example.B, inspect.ismethod))

.. code::

    [('__init__', <unbound method B.__init__>),
     ('do_something', <unbound method B.do_something>),
     ('get_name', <unbound method B.get_name>)]

----

:data-x: r1600
:data-y: 0

Most python objects have a ``.__doc__`` attribute that will let you access doc strings.

.. code:: python

    import example

    print example.B.__doc__

.. code::

    This is the B class.
        It is derived from A.

----

:data-x: r0
:data-y: r900

The ``inspect.getdoc`` function conveniently handles whitespace for you:

.. code:: python

    import inspect
    import example

    print inspect.getdoc(example.B)

.. code::

    This is the B class.
    It is derived from A.

----

Somewhat unbelievably, the ``inspect.getcomments`` function exists.

.. code:: python

    import inspect
    import example

    print inspect.getcomments(example.B.do_something)

.. code::

    # This method is not part of A.

----

That kind of thing is accomplished under the hood by making use of the
``inspect.getsource`` and ``inspect.getsourcelines`` functions.

.. code:: python

    import inspect
    import example

    print inspect.getsource(example.B)

.. code::

    class B(A):
        """This is the B class.
        It is derived from A.
        """

        # This method is not part of A.
        def do_something(self):
            """Does some work"""
            pass

        def get_name(self):
            "Overrides version from A"
            return 'B(' + self.name + ')'


----

:data-x: r1600
:data-y: 0

Other things like ``inspect.getargspec`` and ``inspect.getclasstree`` exist and
are neat, but I'm going to skip them so we can get to:

----

:data-x: r0
:data-y: r900

Good old ``inspect.stack``, my favorite.

.. code:: python

    import inspect

    def f(arg):
        return g(arg + 1)

    def g(arg):
        return h(arg + 2)

    def h(arg):
        for frame, filename, line_num, func, src_code, src_idx in inspect.stack():
            print "In", func, 'where "arg" is', frame.f_locals.get('arg')
        return "Formatted %r" % arg

    print f(2)

.. code::

    In h where "arg" is 5
    In g where "arg" is 3
    In f where "arg" is 2
    In <module> where "arg" is None
    Formatted 5

----

You can use ``inspect.stack`` to do very neat things.  Here's a hypothetical application:

.. code:: python

    import inspect
    import sys


    def get_calling_docstring(i=1):
        """ Return the docstring of the caller i levels above us. """
        frame, _, _, func, _, _ = inspect.stack()[i]
        return inspect.getdoc(frame.f_globals[func])


    def f(argument):
        """ This does the most awesome stuff.

        However, don't call it with "fail", because it will fail.
        """

        if argument == 'fail':
            print get_calling_docstring()
            sys.exit(1)

        # AWESOME

    f('fail')


----

:data-x: r1600
:data-y: 0

Not going to take
=================
a look inside
~~~~~~~~~~~~~

In past presentations, we would open up the code and go through systematically
how it works.  Not so, this time.


.. code::

    $ wc -l /usr/lib64/python2.7/inspect.py
    1058 /usr/lib64/python2.7/inspect.py


It's large.  For context: of the 207 modules in the standard library, it's in
the top %15.

.. code:: bash

    $ wc -l /usr/lib64/python2.7/*.py | sort -n | tail -30
    1005 /usr/lib64/python2.7/mhlib.py
    1011 /usr/lib64/python2.7/rfc822.py
    1046 /usr/lib64/python2.7/ftplib.py
    1050 /usr/lib64/python2.7/cgi.py
    1058 /usr/lib64/python2.7/inspect.py     # <-- homeboy
    1098 /usr/lib64/python2.7/codecs.py
    1332 /usr/lib64/python2.7/threading.py
    1338 /usr/lib64/python2.7/pdb.py
    1342 /usr/lib64/python2.7/httplib.py
    ... <snip> ...
    2230 /usr/lib64/python2.7/mailbox.py
    2274 /usr/lib64/python2.7/pickletools.py
    2350 /usr/lib64/python2.7/pydoc.py
    2361 /usr/lib64/python2.7/argparse.py
    2592 /usr/lib64/python2.7/tarfile.py
    2792 /usr/lib64/python2.7/doctest.py
    6198 /usr/lib64/python2.7/decimal.py


----

:data-x: r0
:data-y: r900

HOWEVER
-------

I'll just mention that the ``inspect.stack()`` implementation is built on top
of ``sys._getframe(1)``:

.. code:: python

    >>> import sys
    >>> frame = sys._getframe(1)
    >>> frame.f_back
    <frame object at 0x19b2b30>
    >>> frame.f_back.f_back
    <frame object at 0x19b2930>
    >>> frame.f_back.f_back.f_back
    <frame object at 0x1949b50>
    >>> frame.f_back.f_back.f_back.f_back
    <frame object at 0x1949960>
    >>> frame.f_back.f_back.f_back.f_back.f_back is None
    True

----

:data-x: r1600
:data-y: r0

This has been
=============

The inspect Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, October 21st, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/inspect/

.. image:: images/creative-commons.png
