:title: stdlib-abc
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The ABC module of
=================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, May 20th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/abc/

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
----------------------

.. image:: http://i.imgur.com/zHz1x6O.gif
   :width: 800px

----

:data-x: r1600
:data-y: 0

.. code:: python

    class SnailMailRouter(object):
        """ Top most object in charge of controlling mail. """

        def route(self, thing):
            for dispatcher in self.dispatchers:
                if dispatcher.can_handle(thing)
                    return dispatcher.dispatch(thing)
            raise ValueError("No dispatcher could handle %r" % thing)

----

:data-x: r0
:data-y: r900

.. code:: python

    class LetterDispatcher(object):
        """ Handles the dispatching of USPS snailmail letters """

        def can_handle(self, thing):
            # This is anything less than 1 ounce (in kilograms)
            return thing.weight < 0.02835

        def dispatch(self, thing):
            if thing.country_code == UNITED_STATES:
                return self.domestic_handler.handle(thing)
            elif thing.country_code in INTERNATIONAL:
                return self.international_handler.handle(thing)

----

.. code:: python

    class BaseDispatcher(object):
        """ To be a dispatcher, you should extend this base class. """

        def can_handle(self, thing):
            raise NotImplementedError("You must override .can_handle()")

        def dispatch(self, thing):
            raise NotImplementedError("You must override .dispatch()")

----

.. code:: python

    class LetterDispatcher(BaseDispatcher):
        """ Handles the dispatching of USPS snailmail letters """

        def can_handle(self, thing):
            # This is anything less than 1 ounce (in kilograms)
            return thing.weight < 0.02835

        def dispatch(self, thing):
            if thing.country_code == UNITED_STATES:
                return self.domestic_handler.handle(thing)
            elif thing.country_code in INTERNATIONAL:
                return self.international_handler.handle(thing)


----

.. code:: python

    class PackageDispatcher(BaseDispatcher):
        """ Handles the dispatching of bigger, bulkier packages """

        def can_handle(self, thing):
            return thing.weight >= 0.02835

        # TODO -- write this some day.
        #def dispatch(self, thing):

.. code:: python

    >>> dispatcher = PackageDispatcher()
    >>> dispatcher.dispatch("something")

.. code::

    Traceback (most recent call last):
      ...
    NotImplementedError: You must override .dispatch()

----

Some problems
=============
with this
~~~~~~~~~

- No traceback until **call** time.
- No calling ``super(...).method(...)``.

----

:data-x: r1600
:data-y: 0

Enter,
======
the abc module
~~~~~~~~~~~~~~

.. image:: https://gs1.wac.edgecastcdn.net/8019B6/data.tumblr.com/tumblr_l7kusvxIlW1qcdqb0o1_500.gif
   :width: 800px

----

:data-x: r0
:data-y: r900

Abstract Base Classes are primarily defined using the ``abc.ABCMeta``
metaclass.

From the `python docs
<https://docs.python.org/2/reference/datamodel.html#customizing-class-creation>`_:

By default, new-style classes are constructed using ``type()``. A class
definition is read into a separate namespace and the value of class name is
bound to the result of ``type(name, bases, dict)``.

When the class definition is read, if ``__metaclass__`` is defined then the
callable assigned to it will be called instead of ``type()``. This allows
classes or functions to be written which monitor or alter the class creation
process:

- Modifying the class dictionary prior to the class being created.
- Returning an instance of another class – essentially performing the role of a
  factory function.

The potential uses for metaclasses are boundless. Some ideas that have been
explored including logging, interface checking, automatic delegation, automatic
property creation, proxies, frameworks, and automatic resource
locking/synchronization.

----


.. code:: python

    import abc

    class BaseDispatcher(object):
        __metaclass__ = abc.ABCMeta

        @abc.abstractmethod
        def can_handle(self, thing):
            """ True if this dispatcher can handle a thing """
            pass

        @abc.abstractmethod
        def dispatch(self, thing):
            """ Send something on its way """
            pass

----

.. code:: python

    class PackageDispatcher(BaseDispatcher):
        """ Handles the dispatching of bigger, bulkier packages """

        def can_handle(self, thing):
            return thing.weight >= 0.02835

        # TODO -- write this some day.
        #def dispatch(self, thing):

.. code:: python

    >>> dispatcher = PackageDispatcher()

.. code::

    Traceback (most recent call last):
      ...
    TypeError: Can't instantiate abstract class PackageDispatcher
               with abstract methods dispatch

----

:data-x: r1600
:data-y: 0

The abc module
==============
about town
~~~~~~~~~~

.. image:: http://i.imgur.com/XxU6Byf.gif
   :width: 800px

The stdlib ``collections`` module defines a number of abstract base classes.

When you ``import collections``, the python builtin types are patched to
register themselves with those abstract bases.  They're like "virtual
subclasses".

----

:data-x: r0
:data-y: r900

General container classes
~~~~~~~~~~~~~~~~~~~~~~~~~

- ``collections.Container``
- ``collections.Sized``

.. code:: python

    >>> isinstance([], collections.Container)
    True
    >>> isinstance('foo', collections.Container)
    True
    >>> isinstance(1, collections.Container)
    False

----

Iterator and Sequence classes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- ``collections.Iterable``
- ``collections.Iterator``
- ``collections.Sequence``
- ``collections.MutableSequence``

.. code:: python

    >>> issubclass(list, collections.Sequence)
    True
    >>> issubclass(list, collections.MutableSequence)
    True
    >>> issubclass(tuple, collections.MutableSequence)
    False
    >>> issubclass(set, collections.MutableSequence)
    False
    >>> issubclass(set, collections.Sequence)
    False
    >>> issubclass(set, collections.Iterable)
    True


----

And more...
~~~~~~~~~~~

Unique values

- ``collections.Hashable``
- ``collections.Set``
- ``collections.MutableSet``

Mappings

- ``collections.Mapping``
- ``collections.MutableMapping``
- ``collections.MappingView``
- ``collections.KeysView``
- ``collections.ItemsView``
- ``collections.ValuesView``

Miscelaneous

- ``collections.Callable``

----

:data-x: r1600
:data-y: 0

Let's take a
============
look inside
~~~~~~~~~~~

.. image:: http://i.imgur.com/zxHLr2e.gif
   :width: 800px

On my system:

.. code::

    ❯ vim /usr/lib64/python2.7/abc.py

----

:data-x: r0
:data-y: r900

The ``@abc.abstractmethod`` decorator is **really** simple:

.. code:: python

    def abstractmethod(funcobj):
        funcobj.__isabstractmethod__ = True
        return funcobj

----

.. code:: python

    class ABCMeta(type):

        def __new__(mcls, name, bases, namespace):
            cls = super(ABCMeta, mcls).__new__(mcls, name, bases, namespace)

            # Compute set of abstract method names
            abstracts = set(name
                         for name, value in namespace.items()
                         if getattr(value, "__isabstractmethod__", False))

            for base in bases:
                for name in getattr(base, "__abstractmethods__", set()):
                    value = getattr(cls, name, None)
                    if getattr(value, "__isabstractmethod__", False):
                        abstracts.add(name)

            cls.__abstractmethods__ = frozenset(abstracts)
            return cls

----

That magic ``__abstractmethods__`` attribute is what actually gets checked.

.. code:: python

    >>> class Wat(object):
    ...     pass

.. code:: python

    >>> Wat.__abstractmethods__ = frozenset(['wut'])
    >>> Wat()

.. code::

    Traceback (most recent call last):
      ...
    TypeError: Can't instantiate abstract class Wat with abstract methods wut

----

The actual check at instantiation-time is performed in Python's C-code.  See
``Objects/typeobject.c`` and look for the ``Py_TPFLAGS_IS_ABSTRACT`` type
flag for the relevant code.

----

.. code:: c

    static int
    type_set_abstractmethods(PyTypeObject *type, PyObject *value, void *context)
    {
        int abstract, res;
        if (value != NULL) {
            abstract = PyObject_IsTrue(value);
            if (abstract < 0)
                return -1;
            res = PyDict_SetItemString(type->tp_dict, "__abstractmethods__", value);
        }
        else {
            abstract = 0;
            res = PyDict_DelItemString(type->tp_dict, "__abstractmethods__");
            if (res && PyErr_ExceptionMatches(PyExc_KeyError)) {
                PyErr_SetString(PyExc_AttributeError, "__abstractmethods__");
                return -1;
            }
        }
        if (res == 0) {
            PyType_Modified(type);
            if (abstract)
                type->tp_flags |= Py_TPFLAGS_IS_ABSTRACT;  // TURNS THE FLAG ON
            else
                type->tp_flags &= ~Py_TPFLAGS_IS_ABSTRACT;
        }
        return res;
    }

----

.. code:: c

    static PyObject *
    object_new(PyTypeObject *type, PyObject *args, PyObject *kwds)
    {
        // <snip> -- some other stuff happens first

        if (type->tp_flags & Py_TPFLAGS_IS_ABSTRACT) {
            /* <snip> - do some stuff that boils down to:
               computing ", ".join(sorted(type.__abstractmethods__))
               into joined_str. */
            PyErr_Format(PyExc_TypeError,
                         "Can't instantiate abstract class %s "
                         "with abstract methods %s",
                         type->tp_name,
                         joined_str);
        }
        return type->tp_alloc(type, 0);
    }

----

Other things left out
~~~~~~~~~~~~~~~~~~~~~

The ``abc.ABCMeta`` metaclass also defines a ``.register(..)`` method so you
can register virtual subclasses.

Also, it overrides ``__instancecheck__(cls, instance)`` and
``__subclasscheck__(cls, subclass)`` so that ``isinstance(instance, cls)`` and
``issubclass(subclass, cls)`` work as you'd expect.

----

:data-x: r1600
:data-y: r0

A Highfive
==========

Give yourself one...

.. image:: http://i.imgur.com/8LpZJzd.gif
   :width: 800px

because now you know all about the ``abc`` module.

----

This has been
=============

The abc Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, May 20th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/abc/

.. image:: images/creative-commons.png
