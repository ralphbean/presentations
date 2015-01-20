:title: stdlib-functools
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The functools module of
=======================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, January 20th, 2015

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/functools/

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

- This is one of a running series of **talks** covering modules from the
  standard library that you may or may not know.  Doug already did this in blog
  form.  I'd like to update and expand it where I can.

----

:data-x: r1600
:data-y: 0

Today we're talking about
=========================
functools
~~~~~~~~~

It's one of the more eye-opening modules.

----

:data-x: r0
:data-y: r900

.. image:: http://i.imgur.com/SJxHr.gif
   :width: 700px

----

Overview
========

We'll talk about it in two major sections:

- decorators
- comparisons

----

:data-x: r1600
:data-y: 0

Decorators Preface (1/5)
========================

Let's talk about functions first.  Functions in python are actually objects!

They **have attributes**.

.. code:: python

    >>> def f(x):
    ...     """ I am a function -- this is my docstring """
    ...     return x + 1
    ...

    >>> f.__doc__
    ' I am a function -- this is my docstring '
    >>> f.func_name
    'f'

    >>> import inspect
    >>> inspect.getargspec(f)
    ArgSpec(args=['x'], varargs=None, keywords=None, defaults=None)

----

:data-x: r0
:data-y: r900

Decorators Preface (2/5)
========================

And you can treat functions like any other object.  You **can return them**!

Consider this function that returns a new function:

.. code:: python

    >>> def adder_maker(add_amount):
    ...     def new_adder(arg1):
    ...         return arg1 + add_amount
    ...     return new_adder

You could use it to make a function that adds four to whatever it is given.

.. code:: python

    >>> four_adder = adder_maker(4)
    >>> four_adder(2)
    6
    >>> four_adder(16)
    20

Even though ``new_adder`` is written only once, it is evaluated afresh each
time ``adder_maker`` is called.  A consequence of this is that different
"adders" returned from ``adder_maker`` don't share scope.

.. code:: python

    >>> five_adder = adder_maker(5)
    >>> five_adder(1)
    6
    >>> four_adder(1)
    5

----

Decorators Preface (3/5)
========================

When you make a function that **both accepts a function and returns one**, you
have made a **decorator**.

Let's make a rilly silly one:

.. code:: python

    >>> def silly(function):
    ...     def replacement_for_that_function(*args, **kwargs):
    ...         value = function(*args, **kwargs)
    ...         return "SILLY! %r" % value
    ...     return replacement_for_that_function

Now, we can apply that to any python function we like, like this:

.. code:: python

    >>> def f(x):
    ...     return x + 1

    >>> f = silly(f)

And when we call it, we get

.. code:: python

    >>> f(41)
    'SILLY! 42'

----

Decorators Preface (4/5)
========================

The ``@silly`` syntax in python is just shorthand for doing ``f = silly(f)``.

.. code:: python

    >>> @silly
    ... def f(x):
    ...     return x + 1

And since the thing returned by ``silly`` is itself a function, you can stack these:

.. code:: python

    >>> @silly
    ... @silly
    ... @silly
    ... def f(x):
    ...     return x + 1

.. code:: python

    >>> f(41)
    'SILLY! "SILLY! \'SILLY! 42\'"'

----

Decorators Preface (5/5)
========================

Another consequence of decorating a function is that since you have actually
replaced your original function with a new one, any special attributes it might
have had are now gone.

.. code:: python

    >>> def decorator(function):
    ...     def wrapper(*args, **kwargs):
    ...         """ This is the wrapper function """
    ...         return function(*args, **kwargs)
    ...     return wrapper

    >>> @decorator
    ... def f():
    ...     """ This is the docstring of f(). """
    ...     return "hello"

    >>> print f.__doc__
    This is the wrapper function


----

:data-x: r1600
:data-y: 0

Decorators with functools (1/4)
===============================

Doug Hellman calls this one "the primary tool provided by functools."  It's neat, but I've never had cause to use it more than once or twice.

``functools.partial(fn, ...)`` takes a function and "partially applies" some arguments, like this:

Consider this function:

.. code:: python

    >>> import functools

    >>> def f(a, b, c, d=4):
    ...     """ This is the function f(...) """
    ...     return "%r %r %r %r" % (a, b, c, d)

Use it like this:

.. code:: python

    >>> p1 = functools.partial(f, 1, 2)
    >>> p1
    <functools.partial object at 0x7f67bf7c1730>
    >>> print p1.__doc__
    partial(func, *args, **keywords) - new function with partial application
        of the given arguments and keywords.
    >>> p1(3)
    '1 2 3 4'

Things can get weird though...

.. code:: python

    >>> p2 = functools.partial(f, b='this is b')
    >>> p2(1, 3)
    Traceback (most recent call last):
      File "<input>", line 1, in <module>
    TypeError: f() got multiple values for keyword argument 'b'
    >>> p2(1, c=3)
    "1 'this is b' 3 4"

----

:data-x: r0
:data-y: r900

Decorators with functools (2/4)
===============================

Remember how I showed that special attributes on a function get lost (or
buried, rather) when you employ decorators?

To carry the docstring and name over to a decorated function, you can use
``functools.update_wrapper``:

.. code:: python

    >>> def f(a, b, c, d=4):
    ...     """ This is the function f(...) """
    ...     return "%r %r %r %r" % (a, b, c, d)

.. code:: python

    >>> p1 = functools.partial(f, 1, 2)
    >>> print p1.__doc__
    partial(func, *args, **keywords) - new function with partial application
        of the given arguments and keywords.

.. code:: python

    >>> p1 = functools.update_wrapper(p1, f)
    >>> print p1.__doc__
     This is the function f(...) 

----

Decorators with functools (3/4)
===============================

To make things easier on you, functools provides its own decorator,
``functools.wraps`` which just applies ``functools.update_wrapper`` for you:

.. code:: python

    >>> def my_decorator(function):
    ...     @functools.wraps(function)
    ...     def wrapper(*args, **kwargs):
    ...         return function(*args, **kwargs)
    ...     return wrapper

.. code:: python

    >>> @my_decorator
    ... def f():
    ...     """ My function """
    ...     return "greatness"

.. code:: python

    >>> f()
    'greatness'
    >>> f.__doc__
    ' My function '

----

Decorators with functools (4/4)
===============================

Lastly, ``functools.update_wrapper`` copies over attributes that it gets from these two constants:

.. code:: python

    >>> functools.WRAPPER_UPDATES
    ('__dict__',)
    >>> functools.WRAPPER_ASSIGNMENTS
    ('__module__', '__name__', '__doc__')

And those cover lots of cases, but there is some weirdness that it doesn't
catch.  I had a program the other week that relied on extracting docstrings
**and** the argspec to produce documentation on a module, and the decorated
functions didn't get their argspecs correctly represented.  (I was using
``functools.wraps``).

There is a third-party module from PyPI I'll recommend named just `decorator
<https://pypi.python.org/pypi/decorator>`_ which does a very good job.

----

:data-x: r1600
:data-y: 0

Comparisons (1/4)
=================

In Python-2.x, classes can define a ``__cmp__(self, another)`` method that
returns -1, 0, or 1 if the object is less than, equal to, or greater than
another.  This is used for sorting lists, etc..

In Python-2.1, the "rich comparison methods API" was introduced that broke this
out into a lot of different methods:  ``__eq__`` for equality, ``__gt__`` for
greater-than, ``__ge__`` for greater-than-or-equal-to, ``__ne__`` for
not-equals, etc...  It is nice, powerful, and flexible.. but it is a pain to
have to define all those methods.

----

:data-x: r0
:data-y: r900

Comparisons (2/4)
=================

Luckily, there is ``functools.total_ordering`` which takes a class with some of
the comparison methods defined, and it fills in the rest.

.. code:: python

    >>> @functools.total_ordering
    ... class MyObject(object):
    ...     def __init__(self, val):
    ...         self.val = val
    ...     def __eq__(self, other):
    ...         print '  testing __eq__(%s, %s)' % (self.val, other.val)
    ...         return self.val == other.val
    ...     def __gt__(self, other):
    ...         print '  testing __gt__(%s, %s)' % (self.val, other.val)
    ...         return self.val > other.val

.. code:: python

    >>> obj1 = MyObject(2)
    >>> obj2 = MyObject(3)

.. code:: python

    >>> print obj1 < obj2
      testing __gt__(2, 3)
      testing __eq__(2, 3)
    True
    >>> print obj1 >= obj2
      testing __gt__(2, 3)
      testing __eq__(2, 3)
    False
    >>> print obj1 > obj2
      testing __gt__(2, 3)
    False

----

Comparisons (3/4)
=================

So, the original ``__cmp__`` method gets **removed** in Python-3.

This means the ``cmp`` argument is *also* removed from things like
``list.sort(cmp=...)`` and ``sorted(list, cmp=...)``.

I used to always do sorts like this:

.. code:: python

    >>> some_dicts = [{'foo': 'bar'}, {'foo': 'abc'}, {'foo': 'zap'}]
    >>> some_dicts.sort(cmp=lambda x, y: cmp(x['foo'], y['foo']))
    >>> some_dicts
    [{'foo': 'abc'}, {'foo': 'bar'}, {'foo': 'zap'}]

Both python-2 and python-3 support a ``key=..`` argument to ``sort()`` and
``sorted()``.  The ``key`` argument should be a function that extracts a key for
comparison in the sort -- a *key extractor*.

We could rewrite the above example to work like this:

.. code:: python

    >>> some_dicts = [{'foo': 'bar'}, {'foo': 'abc'}, {'foo': 'zap'}]
    >>> some_dicts.sort(key=lambda o: o['foo'])
    >>> some_dicts
    [{'foo': 'abc'}, {'foo': 'bar'}, {'foo': 'zap'}]

----

Comparisons (4/4)
=================

functools provides a ``functools.cmp_to_key`` decorator that will convert your
cmp functions into key functions.

.. code:: python

    >>> some_dicts = [{'foo': 'bar'}, {'foo': 'abc'}, {'foo': 'zap'}]

    >>> @functools.cmp_to_key
    ... def comparator(a, b):
    ...     return cmp(a['foo'], b['foo'])

    >>> some_dicts.sort(key=comparator)
    >>> some_dicts
    [{'foo': 'abc'}, {'foo': 'bar'}, {'foo': 'zap'}]

*Wait..* **what**?

How does it do that?


----

:data-x: r1600
:data-y: 0

It does it
==========
like this
~~~~~~~~~

.. image:: http://i.imgur.com/GvIPBPl.gif
   :width: 700px

I didn't see it coming...

----

:data-x: r0
:data-y: r900

It builds a proxy class and returns *that*.  When instances of that class get
compared, their implementations proxy the comparison to the original ``cmp``
function.

.. code:: python

    def cmp_to_key(mycmp):
        """Convert a cmp= function into a key= function"""
        class K(object):
            __slots__ = ['obj']
            def __init__(self, obj, *args):
                self.obj = obj
            def __lt__(self, other):
                return mycmp(self.obj, other.obj) < 0
            def __gt__(self, other):
                return mycmp(self.obj, other.obj) > 0
            def __eq__(self, other):
                return mycmp(self.obj, other.obj) == 0
            def __le__(self, other):
                return mycmp(self.obj, other.obj) <= 0
            def __ge__(self, other):
                return mycmp(self.obj, other.obj) >= 0
            def __ne__(self, other):
                return mycmp(self.obj, other.obj) != 0
            def __hash__(self):
                raise TypeError('hash not implemented')
        return K

----

All-in-all, ``functools.py`` is only 100 lines and is relatively easy to
understand (compare this with the many thousands of lines in modules we
previously covered).

.. code:: bash

    ❯ wc -l /usr/lib64/python2.7/functools.py
    100 /usr/lib64/python2.7/functools.py

Here are how some of the other bits are implemented:

----

.. code:: python

    def total_ordering(cls):
        """Class decorator that fills in missing ordering methods"""
        convert = {
            '__lt__': [('__gt__', lambda self, other: not (self < other or self == other)),
                       ('__le__', lambda self, other: self < other or self == other),
                       ('__ge__', lambda self, other: not self < other)],
            '__le__': [('__ge__', lambda self, other: not self <= other or self == other),
                       ('__lt__', lambda self, other: self <= other and not self == other),
                       ('__gt__', lambda self, other: not self <= other)],
            '__gt__': [('__lt__', lambda self, other: not (self > other or self == other)),
                       ('__ge__', lambda self, other: self > other or self == other),
                       ('__le__', lambda self, other: not self > other)],                   
            '__ge__': [('__le__', lambda self, other: (not self >= other) or self == other),
                       ('__gt__', lambda self, other: self >= other and not self == other),
                       ('__lt__', lambda self, other: not self >= other)]
        }
        roots = set(dir(cls)) & set(convert)
        if not roots:
            raise ValueError('must define at least one ordering operation: < > <= >=')
        root = max(roots)       # prefer __lt__ to __le__ to __gt__ to __ge__
        for opname, opfunc in convert[root]:
            if opname not in roots:
                opfunc.__name__ = opname
                opfunc.__doc__ = getattr(int, opname).__doc__
                setattr(cls, opname, opfunc)
        return cls

----

.. code:: python

    WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__doc__')
    WRAPPER_UPDATES = ('__dict__',)
    def update_wrapper(wrapper,
                       wrapped,
                       assigned = WRAPPER_ASSIGNMENTS,
                       updated = WRAPPER_UPDATES):
        for attr in assigned:
            setattr(wrapper, attr, getattr(wrapped, attr))
        for attr in updated:
            getattr(wrapper, attr).update(getattr(wrapped, attr, {}))
        # Return the wrapper so this can be used as a decorator via partial()
        return wrapper

----

.. code:: python

    def wraps(wrapped,
              assigned = WRAPPER_ASSIGNMENTS,
              updated = WRAPPER_UPDATES):
        return partial(update_wrapper, wrapped=wrapped,
                       assigned=assigned, updated=updated)

----

:data-x: r1600
:data-y: 0

This has been
=============

The functools Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, January 20th, 2015

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/functools/

.. image:: images/creative-commons.png
