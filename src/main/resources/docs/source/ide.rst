.. _IDE:

Basic information and quick start
=================================

.. versionadded:: 1.1.0

Global options - setup and usage
--------------------------------

SikuliX now has a basic implementation of a globel options repository, that is recognized and loaded at startup time (IDE and API usage). At runtime, this options store can be accessed to get and set values. Persistence (save) and merge of additional options files is not yet supported.

Since internally the options are stored in a Java Properties object as key-value-pairs in string representation, the options file must conform to the applicable rules (`look here <http://docs.oracle.com/javase/7/docs/api/java/util/Properties.html#load(java.io.Reader>`_).

At startup these places are searched for a file ``SikulixOptions.txt`` (first appearence wins):
 * the working folder 
 * the user's home folder
 * the folder ``SikulixStore`` in the :ref:`Sikulix repository <SikulixAppData>`
 
**Special for the options defined in the Settings class**
 * see :ref:`Controlling Sikuli Scripts and their Behavior <ControllingSikuliScriptsandtheirBehavior>`
 * any ``Settings.option = value`` will overwrite the existing option value in the Settings class at startup
 
**Other Options globally recognized and processed on startup**

 - ``Debug.level = n`` 
 
     preferably n as 3, debug information produced from beginning of startup
     
 - ``Settings.OverwriteImages = yes``
     
     when saving images in the IDE, usually when using a name, that already exists, this is not overwritten, but  stored with a name having a suffix -n appended.
     With this option switched on (default is off) naming images with an existing name will replace the image file without notice. 
     
 - ``classpath = list of absolute-paths`` (works only up to version 1.1.3)
 
     will be added to the end of the classpath in the given sequence.
     Rules are the same as for Java classpath on that system. On Windows backslashes have to be doubled (escaped).

.. _RunOnlyParts:

.. versionadded:: 1.1.4

Run only parts of a script
--------------------------

(Until noted only available for Jython scripting)

By ``right-clicking`` on a line number the pop-up context menu lets you

 - run only the line at this line number
 - run the script from beginning up to and including this line
 - run the script from this line to the end
 - run the snippet in the current selection or this line if nothing is selected

The ``Run menu`` now contains the entry ``Run selection SHIFT-CTRL/CMD-R``, that also runs the current selection
or the line at the caret, if nothing is selected (advantage: the available shortcut, which saves additional mouse actions)

BE AWARE for Jython: If the first line of a selection has an indent, ``if True:`` is added as first line,
to make the snippet runnable

This feature might be a neat replacement for the no longer available interactive mode from commandline.

.. _EditPython:

.. versionadded:: 1.1.4

Plain Python scripts - edit, load, save and run
-----------------------------------------------

(Until noted only available for Jython scripting)

This is work in progress and `related to this request bug <https://bugs.launchpad.net/sikuli/+bug/1818514>`_



 
