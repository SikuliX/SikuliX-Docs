Sikuli Documentation for version 1.1+ (01/2014 and later)
=========================================================

.. sidebar:: Getting Help

   Looking for specific information?
   
   * Try the :doc:`Table of Contents <toc>` 
   * Look through the :ref:`genindex`
   * Use the :ref:`search`
   
   See `other people's questions <https://answers.launchpad.net/sikuli>`_ 
   or `ask a question <https://answers.launchpad.net/sikuli/+addquestion>`_ yourself.
   
   If you think you've found bugs, search or report bugs in 
   our `bug tracker <https://bugs.launchpad.net/sikuli>`_.

This document is being maintained by `Raimund Hocke aka RaiMan
<https://launchpad.net/~raimund-hocke>`_.  

If you have any questions or ideas about this document, 
you are welcome to contact him directly via this link and the email behind. 

For questions regarding the
functions and features of Sikuli itself please use the `Sikuli Questions and
Answers Board <https://answers.launchpad.net/sikuli>`_.  

For hints and links of
how to get more information and help, please see the sidebar.

**Documentation for previous versions**

The documentation for the versions up to SikuliX-1.0rc3 is still available `here <http://doc.sikuli.org>`_.

How to use this document
------------------------

Sikuli at the top supports scripting via SikuliX IDE (a basic script editor allowing to load, edit, save and run scripts). 

The first scripting language is Python (language level 2.5 (stable) and 2.7 being in a beta state, but rather complete) supported by the Jython interpreter package. 

As a second scripting language Ruby (language level 1.9/2.0) can be used, which internally is supported by the JRuby interpreter package. 

Principally it is possible to implement more scripting languages, if some Java based interpreter package is available for that (possible candidates: BeanShell, Scala, JavaScript, ...).

If you are new to
programming, you can still enjoy using Sikuli to automate simple repetitive
tasks without learning one of the supported scripting languages using the SikuliX IDE. A good start might be to have a look at the :doc:`tutorials <tutorials/index>`.

If you plan to write more powerful and
complex scripts, which might even be structured in classes and modules, you have to dive into the `Python Language
<http://www.jython.org/jythonbook/en/1.0/>`_ or the `Ruby Language <http://docs.ruby-doc.com/docs/ProgrammingRuby>`_.

But be aware: Since the interpreters used are based on Java, the modules available for Python or Ruby might not be available in the Jython or JRuby environment. So before trying to use any non-standard modules are extension packages, you have to check, wether they are supported in this SikuliX environment.

The features in Sikuli at the bottom line are implemented using Java. So you might as well use Sikuli on this Java level API in your Java projects or other Java aware environments (:doc:`see how to <faq/030-java-dev>`). Though this documentation is targeted at the scripting people it contains basic information about the Java level API as well at places, where there are major differences between the two API levels. Additionally you might look through the JavaDocs (`temporary location <https://dl.dropboxusercontent.com/u/42895525/docs/index.html>`_). 

The preface of each chapter in this documentaton briefly describes 
a class or a group of methods regarding its basic features. It
provides general usage information and hints that apply to all methods in 
that chapter. We recommend to read carefully before using the related features.

**If you are totally new with Sikuli**, it might be a good idea to just read
through this documentation sequentially. An alternative way might be to jump to the
chapters that you are interested in by scanning the :doc:`table of contents <toc>`. 
A way in the middle would be reading the core classes:
:py:class:`Region`, then :py:class:`Match`, and finally :py:class:`Screen`.

After that, you can go to any
places of interest using the :doc:`table of contents<toc>` or 
use the :ref:`genindex` to browse all classes, 
methods and functions in alphabetical order.

Getting Started
---------------

Tutorials
^^^^^^^^^

.. toctree::
   :maxdepth: 2

   tutorials/index

FAQ
^^^
.. toctree::
   :maxdepth: 1
   :glob:

   faq/*
   
* `Read more FAQs on Launchpad <https://answers.launchpad.net/sikuli/+faqs>`_

Complete Guide
--------------
.. toctree::
   :maxdepth: 3

   sikuli-script-index

Extensions
----------
.. toctree::
   :maxdepth: 2

   extensions/index

For Developers
--------------

.. toctree::
   :maxdepth: 2
   :glob:

   devs/*
   contributing
   changes
