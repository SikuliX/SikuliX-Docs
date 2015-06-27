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

.. _UsingPython:

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
    

.. _LoadableJars:

Prepare and use your own jar files in the Jython environment
------------------------------------------------------------

You might prepare jar files containing Python scripts/modules/packages, Java classes and other stuff like images, that are intended to be used in the scripting context.

**possible use cases**
 - you want to pack scripted stuff together with other resources into a container ready to be used by yourself or others via import (which is not supported by the .skl packaging method).
 - you want to secure your script code against modifications by others, that use your distributed jar.
 
Later (possibly only with version 2) there will be a feature available, to run such script containers directly from commandline (``java -jar mystuff.jar parameters``) or by double clicking.

**typical jar file structure**::

    -- jar rootlevel
    module1.py    # Python module
    module2.py
    - folder1     # Python package
      __init__.py
      stuff1.py
      stuff2.py
    - images      # image folder
      img1.png
      img2.png
    - org         # Java package
      - mystuff
        class1.class
        class1.class

**how to pack such a jar**

You might use the Java ``jar utility`` (contained in the JDK).

Or use the **SikuliX provided** feature ``Sikulix.buildJarFromFolder(jarpath, folder)``, where jarpath is the absolute path to the jar (the parent folder must exist, the jar is overwritten), that should be created and folder is the absolute path to a folder, containing the stuff to be packed. The content of the folder is copied to the root of the created jar.

Just run ``Sikulix.buildJarFromFolder(jarpath, folder)`` in an empty tab in the IDE or in a script, that might do some pre- and/or postprocessing.

If the folder contains an ``__init__.py`` on the first level, the given folder is taken as a Python package and as such copied to the root level of the jar, to preserve the package context::

    -- packagefolder
      __init__.py
      stuff.py
      
    becomes a jar
    -- jar rootlevel
    - packagefolder
      __init__.py
      stuff.py
 
**how to secure your script code using the jar packaging**
 - Step 1: prepare a folder as in the previous chapter
 - Step 2: compile the folder into a new folder (see below)
 - Step 3: pack the new folder into a jar for distribution
 
Run in an empty IDE tab or as part of a script:

``Sikulix.compileJythonFolder(sourcefolder, targetfolder)`` 

copies the complete content from sourcefolder to targetfolder (the parent folder must exist, the folder is emptied if exists) and then traverses the targetfolder replacing each ``foobar.py`` with it's compiled version ``foobar$py.class``, that contains JVM-byte-code, so your script code cannot be edited anymore in this targetfolder, but still be used with ``import foobar``.

**Be aware:** Be sure, your code compiles without errors, because the compile feature either succeeds or fails (compile errors), but you will not get any information about the cause or even the place of the compile problem.
 
Using Ruby
==========

Using SikuliX in Java programming
=================================

Using SikuliX in non-Java programming scenarios
===============================================



