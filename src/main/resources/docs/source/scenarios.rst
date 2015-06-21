SikuliX - general aspects of scripting
======================================

What is a SikuliX script
------------------------

Where and how can a SikuliX script be stored
--------------------------------------------

How to run a SikuliX script or a series of scripts
--------------------------------------------------

Using JavaScript
================

Using Python
============

Setup a Jython environment
--------------------------

**This only applies to SikuliX 1.1.0+ with Jython 2.7.0+**

**Note for Mac OSX**
If you ever encounter an error like ``ValueError: unknown locale: UTF-8``, then take care, that your environment at runtime of Jython contains these 2 entries:
 - LC_ALL=en_US.UTF-8
 - LANG=en_US.UTF-8
You might use ``export`` or any other appropriate method.

In cases you do not want to run scripts from inside the SikuliX IDE or from command line using the SikuliX command scripts or jar-files, you might setup your own Jython environment and run scripts.

Apply the following steps, to get a Jython environment, that is SikuliX aware:

 - download the installer package from `Jython Downloads <http://www.jython.org/downloads.html>`_
 - install (usually by double-clicking the package) using the standard setup into an empty folder 
 - test by running ``<jython-folder>/bin/jython`` from a commandline, which should open an interactive Jython session, that allows, to run Python statements line by line
 - make sure, that pip and easy_install are available:
  - ``<jython-folder>/bin/pip`` exists
  - ``<jython-folder>/bin/easy_install`` exists
  - if this is not the case run ``<jython-folder>/bin/jython -m ensurepip`` on a commandline and check again
  - if this is still not the case follow the steps further below ``Fallback without pip``
 - run ``<jython-folder>/bin/pip install jip`` to install the package ``jip``, which allows to add Java libraries easily to your Jython environment 
 - add any needed Python package (must not depend on C-based stuff) using ``pip``, ``easy-install`` or manual methods into ``<jython-folder>/Lib/site-packages`` and/or use ``jip`` for adding Java libraries preferably from Maven Central 
Access Python packages from SikuliX scripts run by SikuliX (GUI or commandline)
-------------------------------------------------------------------------------

The following approaches apply to situations, where you want to use Python modules installed somewhere on your system, without the need to manipulate ``sys.path``, meaning, that when using ``ìmport moduleXYZ`` this package is found automatically.

SikuliX uses a central repository (``SikulixRepo`` in the following) for internal stuff (native libraries, downloaded artifacts, resources needed at runtime and simailar things). This is a folder in the user's private space (home folder):
 - Windows: ``%APPDATA%\Sikulix``
 - Mac: ``~/Library/Application Support/Sikulix``
 - Linux: ``~/.Sikulix``

**Basic preparation**
    To ``SikulixRepo`` add a folder ``Lib`` (if not already there) and inside add a folder ``site-packages``
    
**Approach 1**
    Since an existing folder ``SikulixRepo/Lib/site-packages`` will be recognized and added automatically as the ``1st entry to sys.path``, modules/packages contained in here will be found when imported without any further preperations. This approach can be used, to "overwrite" modules/packages, that otherwise would be found elsewhere on ``sys.path`` (e.g. for testing)
    
**Approach 2**
    In the folder ``SikulixRepo/Lib/site-packages`` have a file ``sites.txt``, that contains absolute paths one per line, that point to other places, where modules packages can be found. These paths will be added automatically at startup to the ``end of sys.path`` in the given sequence. With this approach, you might for example add the ``Lib/site-packages`` folder of your own Jython installation.
    
Using Ruby
==========

Using SikuliX in Java programming
=================================

Using SikuliX in non-Java programming scenarios
===============================================



