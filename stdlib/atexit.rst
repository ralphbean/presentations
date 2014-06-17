:title: stdlib-atexit
:css: css/style.css
:data-transition-duration: 500
:skip-help: true
:hovercraft-path: m275,175 v-150 a150,150 0 0,0 -150,150 z

----

The atexit module of
====================

the Standard Library
~~~~~~~~~~~~~~~~~~~~

RocPy, June 17th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/atexit/

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

.. image:: http://i.minus.com/ibnMfO5dXlo1FG.gif
   :width: 600px

----

:data-x: r1600
:data-y: 0


The atexit module
=================
is straightforward to use
~~~~~~~~~~~~~~~~~~~~~~~~~

All you need is ``atexit.register(...)``.

.. code:: python

    import atexit

    def some_function():
        print "some_function() is called"

    print "registering some_function()"
    atexit.register(some_function)
    print "end of main program..."

.. code::

    registering some_function()
    end of main program...
    some_function() is called

----

:data-x: r0
:data-y: r900

And it can only get *slightly* more complicated than that.

.. code:: python

    import atexit

    def some_function(some_argument):
        print "some_function() is called with", some_argument

    print "registering some_function()"
    atexit.register(some_function, "boil 'em")
    atexit.register(some_function, "fry 'em")
    atexit.register(some_function, "stick 'em in a stew")
    print "end of main program..."

.. code::

    registering some_function()
    end of main program...
    some_function() is called with stick 'em in a stew
    some_function() is called with fry 'em
    some_function() is called with boil 'em

----

You should also know about ``sys.exitfunc`` as well.  You can assign any
function to it and it will be called at shutdown, just like with ``atexit``.

Using ``sys.exitfunc`` directly supports only one function, whereas ``atexit``
supports as many as you like.

----

So
==
simple
~~~~~~

It's so simple.  ``atexit.register(..)`` calls your callables when the process
exits.

----

:data-x: r1600
:data-y: 0

But,
====
not always
~~~~~~~~~~

.. image:: http://i.imgur.com/Xh865vt.gif
   :width: 700px

----

:data-x: r0
:data-y: r900

The ``atexit`` functions you registered won't be called in these cases:

- the program dies because of a signal
- ``os._exit()`` is invoked directly (as opposed to ``os.exit()``).
- a Python fatal error is detected (in the interpreter)

It furthermore won't be called at the end of a Thread's execution (the
resultant suffering of which I documented in `this blog post
<http://threebean.org/blog/atexit-for-threads/>`_).  Fun fact, it is the root
cause of my first need to ever look at the C-code of CPython.

----

:data-x: r1600
:data-y: 0

The atexit module
=================
about town
~~~~~~~~~~

.. image:: http://i.imgur.com/0NysV2K.gif
   :width: 800px

In last weeks' presentation, I was able to find only one place in which
*another* stdlib module *used* the module we were studying (that was the
``abc`` module).

In this week's presentation, we have a better story.  The ``logging`` and
``multiprocessing`` modules both make use of ``atexit`` to clean themselves up.


----

:data-x: r0
:data-y: r900

logging
~~~~~~~


.. code:: python

    def shutdown(handlerList=_handlerList):
        for wr in reversed(handlerList[:]):
            h = wr()
            if h:
                try:
                    h.acquire()
                    h.flush()
                    h.close()
                except (IOError, ValueError):
                    pass
                finally:
                    h.release()

    #Let's try and shutdown automatically on application exit
    import atexit
    atexit.register(shutdown)

----

multiprocessing
~~~~~~~~~~~~~~~

.. code:: python

    def _exit_function():
        info('process shutting down')

        for p in active_children():
            if p._daemonic:
                info('calling terminate() for daemon %s', p.name)
                p._popen.terminate()

        for p in active_children():
            info('calling join() for process %s', p.name)
            p.join()

    atexit.register(_exit_function)

----

And more...
~~~~~~~~~~~

.. code:: bash

    $ find /usr/lib64/python2.7/ -name "*.py" \
        -exec grep atexit {} \; | \
        wc -l
    108

108 references to atexit in other third-party libraries I have installed on my system.

----

:data-x: r1600
:data-y: 0

Let's take a
============
look inside
~~~~~~~~~~~

.. image:: http://i.imgur.com/JNXq7wj.gif
   :width: 700px

----

:data-x: r0
:data-y: r900

On my system:

.. code::

    ❯ vim /usr/lib64/python2.7/atexit.py

----

:data-x: r0
:data-y: r900

The ``atexit.register(...)`` function is **really** simple:

.. code:: python

    _exithandlers = []

    def register(func, *targs, **kargs):
        _exithandlers.append((func, targs, kargs))
        return func

----

The bulk of the work is done by a ``_run_exitfuncs()`` function:

.. code:: python

    def _run_exitfuncs():
        while _exithandlers:
            func, targs, kargs = _exithandlers.pop()
            func(*targs, **kargs)

----

Lastly, the pieces are sewn together by this module-level code that
monkey-patches the ``sys.exitfunc`` callable (remember the one I mentioned
earlier?)

.. code:: python

    if hasattr(sys, "exitfunc"):
        # Assume it's another registered exit function - append it to our list
        register(sys.exitfunc)

    sys.exitfunc = _run_exitfuncs


----

The code responsible for calling that ``sys.exitfunc()`` handler lives in the C
code of CPython in ``Python/pythonrun.c`` , specifically in the ``Py_Finalize``
function (the one that is called to do the work of shutting down the
interpreter).

----

.. code:: c

    void
    Py_Finalize(void)
    {
        /* This does a few preliminary things, like waiting on threads, until */

        call_sys_exitfunc();

        /* ... this goes on and on and does a lot more stuff... */
    }

----

.. code:: c

    static void
    call_sys_exitfunc(void)
    {
        PyObject *exitfunc = PySys_GetObject("exitfunc");

        if (exitfunc) {
            PyObject *res;
            Py_INCREF(exitfunc);
            PySys_SetObject("exitfunc", (PyObject *)NULL);
            res = PyEval_CallObject(exitfunc, (PyObject *)NULL);
            if (res == NULL) {
                if (!PyErr_ExceptionMatches(PyExc_SystemExit)) {
                    PySys_WriteStderr("Error in sys.exitfunc:\n");
                }
                PyErr_Print();
            }
            Py_DECREF(exitfunc);
        }

        if (Py_FlushLine())
            PyErr_Clear();
    }

----

:data-x: r1600
:data-y: r0

This has been
=============

The atexit Module of the Standard Library
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RocPy, June 17th, 2014

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``

- Slides: http://threebean.org/presentations/stdlib/atexit/

.. image:: images/creative-commons.png
