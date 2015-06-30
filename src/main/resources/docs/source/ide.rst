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
     
 - ``classpath = list of absolute-paths``
 
     will be added to the end of the classpath in the given sequence. Rules are the same as for Java classpath on that system. On Windows backslashes have to be doubled (escaped).
 
