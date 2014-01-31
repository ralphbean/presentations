:title: GPG Key-Signing Party
:css: css/style-redhat.css

This is my first hovercraft presentation.
It was for BarCamp Rochester, November 2013.

----

GPG Key-Signing Party
---------------------

Barcamp Rochester, November 2nd, 2013

- Presented by Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/gpg/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png

----

What is GPG (wikipedia quote time)
----------------------------------

- Pretty Good Privacy (**PGP**) is a data encryption and decryption program
  that provides cryptographic privacy and authentication for data
  communication. **PGP** is often used for signing, encrypting, and decrypting
  texts, e-mails, files, directories, and even whole disk partitions.

- GNU Privacy Guard (GnuPG or **GPG**) is a GPL Licensed alternative to the PGP
  suite of cryptographic software.

- **X.509** is an ITU-T standard for a public key infrastructure (PKI) and 
  a Privilege Management Infrastructure (PMI).
  It specifies, amongst other things, standard formats for public key
  certificates, certificate revocation lists, attribute certificates,
  and a certification path validation algorithm.
  It assumes a strict hierarchical system of certificate authorities (CAs)
  for issuing the certificates.

----

Cases where you might use GPG
-----------------------------

- Signing communications

  - You are a developer.  Your open source colleague receives an email with
    your from address stating::

       > Oh hai.  That 1.0.4 tarball we just released has a crucial bug.  Can
       > you quickly cut and post a 1.0.5 with the fixes I just pushed to
       > git?  THNXXX!  --slappy

  - This message could be spoofed by someone else that, say, has gained
    escalated privileges on your SCM, but not your release infrastructure.
    They have pushed malware into your repo and rebased to hide it, but hope
    that the release-manager colleague won't notice if the email appears to be
    from you.

    If you *usually* GPG sign your emails, your colleague is more likely to
    notice something is wrong and investigate first.

- Encrypting communications

  - You are a sysadmin and you need to email someone a password.
    Do it in plaintext?  **NO**.  Encrypt the message using the *public key*
    of the *recipient*.

- Signing data

  - I upload tarballs of project releases to PyPI (the python package index)
    but I make sure to sign them with my personal key first.  Some projects
    use a project-specific key shared by the release managers.

- Encrypting data

  - Sometimes you want to share a dump of a production database with another
    developer to hack on locally.  You can post it to the web, but, private
    data?  Encrypt the tarball with the *public key* of the *recipient*.

----

Visualizing the Web of Trust
----------------------------

.. image:: http://threebean.org/pubring.gif
   :width: 500px
   :height: 500px

::

    $ gpg --list-sigs --keyring ~/.gnupg/pubring.gpg | sig2dot > ~/.gnupg/pubring.dot
    $ neato -Tps ~/.gnupg/pubring.dot > ~/.gnupg/pubring.ps
    $ convert ~/.gnupg/pubring.ps ~/.gnupg/pubring.gif
    $ eog ~/.gnupg/pubring.gif

----

What signing another key means
------------------------------

- It does mean that you trust that the key belongs to the person mentioned in
  the ``UserID`` field.

- It does *not* mean that you trust that person.

  - Say someone sends you an executable, signed with their key, and says
    "run this, plz".  You don't know them, but you see that *I* have signed
    their key.  Do not think, "Ralph trusts this person, therefore I can
    trust that what they say is not malicious."

----

Let's do it!
------------

Creating your first GPG key
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Choosing default options are fine.  Make sure to choose a passphrase::

    $ gpg --gen-key

Take a look in ``~/.gnupg/`` to see what it created.

Your fingerprint can be found with::

    $ gpg --fingerprint jqdoe@example.com

Upload your *public* key to a keyserver (there are many such)::

    $ gpg --keyserver hkp://subkeys.pgp.net --send-key KEYNAME

----

Let's do it!
------------

Signing others' keys
~~~~~~~~~~~~~~~~~~~~

There is a good way to run a key signing party, and an easy way.  We're
going to do the easy way since there was no pre-registration.  See `this
page <http://www.phillylinux.org/keys/terminal.html>`_ for a good description
of the better way.

We'll sit in an oblong circle with each person across from another.  Pass your
identification to the person across from you and tell them your fingerprint
hash.  For the purposes of this slide, we'll use my fingerprint:  ``971095FF``.

Get their public key from the key server::

    $ gpg --keyserver hkp://subkeys.pgp.net --recv-keys 971095FF

Begin the process of signing their key.  *Verify* that the name on the key
matches the identification provided.  Legit?

::

    $ gpg --sign-key 971095FF

Send the signed copy of their key back to the keyserver::

    $ gpg --keyserver hkp://subkeys.pgp.net --send-key 971095FF

The problem here is that we have verified that the *name* on their key
matches the name on their identification, but we haven't verified that
they *have control* over the email address listed on their key.  See
the link above for how to do that.  Forgive me.

Once others have signed *your* key and sent it back to the key-server, you
can import *their* signatures with::

    $ gpg --keyserver hkp://subkeys.pgp.net --recv-keys YOUR_KEY

----

*fín*
-----

Presented by:

- Ralph Bean
- http://github.com/ralphbean
- http://twitter.com/ralphbean
- http://threebean.org
- ``2048R/971095FF 2012-12-06``
- ``9450 4C3A E11D D197 9200 58AB A90E D7DE 9710 95FF``

Slides available at http://threebean.org/presentations/gpg/

.. image:: http://i.creativecommons.org/l/by-sa/3.0/88x31.png
