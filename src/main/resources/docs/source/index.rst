Sikuli / SikuliX Documentation for version 1.1+ (2014 and later)
================================================================

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
you are welcome to contact him directly via the above link and the email behind. 

**QuickStart information and other possibilities to get in contact:** `sikulix.com <http://sikulix.com>`_.

For questions regarding the functions and features of Sikuli itself, that are not answered
sufficiently in this documentation, please use the `Sikuli Questions and
Answers Board <https://answers.launchpad.net/sikuli>`_.  

For hints and links of
how to get more information and help, please see the sidebar.

**Documentation for previous versions**

Might not be available any longer without notice or might not work properly. Feel free to post a bug in case.

The documentation for the versions up to SikuliX-1.0rc3 is still available `here <http://doc.sikuli.org>`_.

How to use this document
------------------------

SikuliX at the top supports **scripting via SikuliX IDE** (a basic script editor to load, edit, save and run scripts including the creation/organization of the needed images for your visual workflow). 

Supported scripting languages:

 - **Python** (language level 2.7) supported by the `Jython interpreter <http://www.jython.org>`_. 

 - **Ruby** (language level 1.9/2.0) supported by the `JRuby interpreter <http://jruby.org>`_.
 
 - **JavaScript** supported by the Java builtin scripting engine (Java 7: Rhino, Java 8: Nashorn).

If you are new to
programming, you can still enjoy using SikuliX to automate simple repetitive
tasks without learning one of the supported scripting languages using the SikuliX IDE.
  A good start might be to have a look at the :doc:`tutorials <tutorials/index>`.

If you plan to write more
complex scripts, which might even be structured in classes and modules, you have to dive into the `Python Language
<http://www.jython.org/jythonbook/en/1.0/>`_, the `Ruby Language <http://docs.ruby-doc.com/docs/ProgrammingRuby>`_ 
or `JavaScript <https://developer.mozilla.org/en-US/docs/Web/JavaScript>`_.

**NOTE:** Since **Jython and JRuby are based on Java**, the modules available for Python or Ruby 
might not be available in the Sikulix environment. 
So before trying to use any non-standard modules or extension packages, 
you have to check, wether they are supported in this SikuliX environment.

**NOTE on Java usage** The features in SikuliX at the bottom line are implemented with Java. 
So you might as well use SikuliX at this Java API level in your Java projects or other Java aware environments (:doc:`see how to <faq/030-java-dev>`). Though this documentation is targeted at the scripting people it contains basic information about the Java level API as well at places, where there are major differences between the two API level.
  Additionally you might **look through the JavaDocs** 
  (`latest version <https://raiman.github.io/SikuliX1/javadocs/overview-summary.html>`_). 

Each chapter in this documentaton briefly describes 
a class or a group of methods regarding their basic features.
General usage information are provided and hints, that apply to all methods in 
that chapter. We recommend to read carefully before using the related features.

**If you are totally new with Sikuli**, it might be a good idea to just read
through this documentation sequentially. An alternative way might be to jump to the
chapters that you are interested in by scanning the :doc:`table of contents <toc>`. 
A way in the middle would be reading the core classes in this sequence:
:py:class:`Region`, then :py:class:`Match`, and finally :py:class:`Screen`.

After that, you can go to any
places of interest using the :doc:`table of contents<toc>` or 
use the :ref:`genindex` to browse all classes, 
methods and functions in alphabetical order.

New Features and major changes (version 1.1.4+)
-----------------------------------------------

.. toctree::
   :maxdepth: 2
   
   news
   newslinux
   newsbugs
   
SikuliX - how does it work and system specific information
----------------------------------------------------------

.. toctree::
   :maxdepth: 2
   
   basicinfo

SikuliX scripting and usage in programming scenarios (preferably Java based)
----------------------------------------------------------------------------

.. toctree::
   :maxdepth: 2
   
   scenarios
   
SikuliX IDE create/run scripts and organize your images 
-------------------------------------------------------

.. toctree::
   :maxdepth: 2
   
   ide

SikuliX API for scripting and Java programming
----------------------------------------------
.. toctree::
   :maxdepth: 2

   scripting
   interaction
   globals
   region
   location
   screen
   pattern
   match
   finder
   keys
   appclass
   textandocr
   
Miscellanous
------------

.. toctree::
   :maxdepth: 2
   :glob:

   faq/*
   extensions/index
   
Tutorials
---------

.. toctree::
   :maxdepth: 1

   tutorials/helloworld/helloworld-mac
   tutorials/helloworld/helloworld-win
   tutorials/goodbyetrash/goodbyetrash 
   tutorials/uncheckall/uncheckall
   tutorials/checkone/checkone
   tutorials/sliders/sliders
   tutorials/surveillance/surveillance
   tutorials/resize/resize

For Developers
--------------

.. toctree::
   :maxdepth: 2
   :glob:

   devs/*
   contributing
