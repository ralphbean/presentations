:title: ansible
:css: css/style-ansible.css
:data-transition-duration: 500
:skip-help: true


----

ansible
-------

RocPy, November 17th, 2015

- Presented by `@ralphbean <http://threebean>`_
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/ansible/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

----

the ansible
-----------
namesake
~~~~~~~~

From `wikipedia <https://en.wikipedia.org/wiki/Ansible>`_::

> An ansible is a fictional machine capable of instantaneous or superluminal
> communication. It can send and receive messages to and from a corresponding
> device over any distance whatsoever with no delay. Ansibles occur as plot
> devices in science fiction literature.

But, as `@moondoggy <https://github.com/adamhayes>`_ pointed out in ``#rit-foss``::

> There is no way to build an ansible based on current technology.  The theory
> of special relativity (and equally well the theory of general relativity)
> predicts that some proposed methods of creating such devices would cause
> communication from the future to the past -- a form of time travel which in
> general raises problems of causality (but not that it couldn't be overcome
> based on other methods).

So what gives, threebean?

----

really,
-------
though...
~~~~~~~~~

From a `different wikipedia page
<https://en.wikipedia.org/wiki/Ansible_(software)>`_::

> Ansible is a free software platform for configuring and managing computers. It
> combines multi-node software deployment, ad hoc task execution, and
> configuration management. It manages nodes over SSH or PowerShell and requires
> Python (2.4 or later) to be installed on them. Modules work over JSON and
> standard output and can be written in any programming language. The system uses
> YAML to express reusable descriptions of systems.


----

Michael
-------
Dehaan
~~~~~~

.. image:: http://allthingsopen.org/wp-content/uploads/2014/08/rs_michael_dehaan.jpg

- Wrote ``func``, ``cobbler``, ``ansible``, and `other cool things <http://michaeldehaan.net/software>`_.

----

Seth
----
Vidal
~~~~~

.. image:: https://pbs.twimg.com/profile_images/1677665352/seth-icon.jpg

- Wrote ``yum`` back in the day.
- Wrote ``func`` with Michael and was involved in getting ``ansible`` off the ground.

----

I use ansible too!
------------------

.. image:: http://threebean.org/img/wat.jpg
   :width: 600px

- but I haven't done anything cool.

----

health benefits
---------------
of ansible
~~~~~~~~~~

- agentless
- simple pki
- simple configuration (yaml, jinja)
- batteries-included (see `the docs <https://docs.ansible.com/ansible/modules_by_category.html>`_)
- extensible

  - modules
  - callback plugins
  - lookup plugins

----

How does it
-----------
compare?
~~~~~~~~


+------------+------------------+----------------+
|            | puppet           | ansible        |
+============+==================+================+
| category   | config management| orchestration  |
+------------+------------------+----------------+
| infra      | puppetmaster     | none required  |
+------------+------------------+----------------+
| direction  | pull             | push           |
+------------+------------------+----------------+
| crypto     | own PKI          | ssh keys       |
+------------+------------------+----------------+
| written in | ruby             | python         |
+------------+------------------+----------------+
| language   | ruby dsl         | yaml           |
+------------+------------------+----------------+
| templates  | ERB              | jinja2         |
+------------+------------------+----------------+
| config     | declarative      | imperative     |
+------------+------------------+----------------+
| resources  | dependency graph | deps only at   |
|            | model            | the role level |
+------------+------------------+----------------+
| extension  | ruby             | any language   |
+------------+------------------+----------------+

----

Modes of
--------
operation (1)
~~~~~~~~~~~~~

- Direct ``ansible`` invocation.

.. code:: bash

    $ ansible all -m ping
    $ ansible all -m ping -u bruce -b --become-user batman

.. code:: bash

    $ ansible db-servers -a "systemctl stop postgres" --limit staging
    $ ansible websites -a "poweroff" --limit staging
    $ ansible bastion -a "wall staging is offline"

----

Modes of
--------
operation (2)
~~~~~~~~~~~~~

- Using ``ansible-playbook`` for more complex endeavors.

.. code:: bash

    $ ansible-playbook ./master.yml
    $ ansible-playbook ./playbooks/reboot.yml --limit db-servers
    $ ansible-playbook ./playbooks/update_dns.yml
    $ ansible-playbook ./playbooks/dangerzone/death_to_postfix.yml -e "target=all"

----

Modes of
--------
operation (3)
~~~~~~~~~~~~~

- Writing scripts that call the ansible python API.

.. code:: python

    import ansible.runner

    # Use the 'virt' module on all hosts in the 'virt-hosts' group.
    kwargs = dict(
        pattern='virt-hosts',
        module_name='virt',
        module_args='command=nodeinfo',
    )
    runner = ansible.runner.Runner(**kwargs)
    result = runner.run()
    for name, host in result['contacted'].items():
        print name, "has", host['cpus'], 'cpus.'
        print name, "has", host['physmemory'], 'physical memory.'

----

A playbook for
--------------
Shellshock
~~~~~~~~~~

.. code:: yaml

    - hosts: all
      gather_facts: yes
      serial: "50%"
      tasks:
        - name: Update Shellshock (Debian)
          apt: name=bash
               state=latest
               update_cache=yes
          when: ansible_os_family == "Debian"

        - name: Update Shellshock (RedHat)
          yum: name=bash
               state=latest
               update_cache=yes
          when: ansible_os_family == "RedHat"

----

A playbook for
--------------
Heartbleed
~~~~~~~~~~

.. code:: yaml

    - hosts: all
      gather_facts: yes
      serial: "25%"
      tasks:
        - name: Update OpenSSL and OpenSSH (Debian)
          apt: name={{ item }}
               state=latest
               update_cache=yes
          with_items:
            - openssl
            - openssh-client
            - openssh-server
          when: ansible_os_family == "Debian"
          notify: reboot the host

        - name: Update OpenSSL and OpenSSH (RedHat)
          yum: name={{ item }}
               state=latest
               update_cache=yes
          with_items:
            - openssl
            - openssh-client
            - openssh-server
          when: ansible_os_family == "RedHat"
          notify: reboot the host

----

It's fun to build
-----------------
your own lightsaber
~~~~~~~~~~~~~~~~~~~

Take a look at my `lightsaber repo <https://github.com/ralphbean/lightsaber>`_.

- secrets management
- a look at laptop setup
- a look at cloud nodes

----

Let's close with
----------------
Fedora Infrastructure
~~~~~~~~~~~~~~~~~~~~~

Take a look at our `ansible repo
<https://infrastructure.fedoraproject.org/cgit/ansible.git/>`_:

- vhost-info
- groups playbooks
- upgrade playbooks
- virthost update playbooks
- rbac-playbook
- proxies, and the includes

----

Questions?
----------

Some links:

- http://www.ansible.com
- https://docs.ansible.com
- https://infrastructure.fedoraproject.org/cgit/ansible.git/
- https://github.com/ralphbean/lightsaber/

- Presented by `@ralphbean <http://threebean>`_
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/ansible/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png
