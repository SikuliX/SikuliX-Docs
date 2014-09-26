.. _General:

General Settings and Access to Environment Information
======================================================

**Java Level**

Java maintains a global storage for settings (key/value pairs), that can be accessed
by the program/script. Sikuli uses it for some of it's settings too. Normally it is
not necessary to access these settings at the Java level from a Sikuli script, since
Sikuli provides getter and setter methods for accessing values, that make sense for
scripting. One example is the list of paths, that Sikuli maintains to specify
additional places to search for images (please refer to :ref:`Importing
other Sikuli Scripts <ImportingSikuliScripts>` for more information).

If needed, you may access the java settings storage as shown in the following
example::

	import java.lang.System 
	
	# get a value
	val = System.getProperty("key-of-property")
	
	# set a property's value
	System.setProperty("key-of-property", value)

**Jython/Python Level**

You may use all settings, that are defined in standard Python/Jython and that are
available in your system environment. 

The modules ``sys`` and ``time`` are already imported,
so you can use their methods without the need for an import statement.

``sys.path`` may be one of the most valuable settings, since it is used by
Python/Jython to locate modules, that are referenced using ``import module``. 
It is a list of path's, that is e.g. maintained by Sikuli to implement 
:ref:`Importing other Sikuli Scripts <ImportingSikuliScripts>` 
as a standard compliant feature (exception: .Sikuli scripts cannot form a module tree).

If you want to use ``sys.path``, it is recommended to do it as shown in the following
example, to avoid appending the same entry again::

	myPath = "some-absolute-path"
	if not myPath in sys.path:
		sys.path.append(myPath)

**Sikuli Level**

Sikuli internally uses the class :py:class:`Settings` to store globally used
settings. Publicly available attributes may be accessed by using
``Settings.[name-of-an-attribute]`` to get it's value and ``Settings.attribute = value``
to set it. It is highly recommended to only modify attributes, that are described in
this document or when you really know, what you are doing.

Actually all attributes of some value for scripting are described in the 
topic :ref:`Controlling Sikuli Scripts and their Behavior <ControllingSikuliScriptsandtheirBehavior>`.

To store some **settings across SikuliX IDE sessions**, SikuliX utilizes the Java feature 
Preferences. 

As persistent storage Java uses:

* on Windows the registry branch HKCU\Software\JavaSoft\Prefs\org\sikuli\...

* on Mac a plist file in ~/Library/Preferences/org.sikuli.....plist

* on Linux usually at ~/.java/.userPrefs/org/sikuli/prefs.xml

The content is :ref:`controlled by the IDE's Preferences panel <IDE>`.
It is safe to delete this branch/file, to get a default setup and might help in
some situations, wher the startup of the IDE does not work or crashes.

Store some of your information persistently and reload it later (same machine)
------------------------------------------------------------------------------

You might use SikuliX's persistent storage, to **store and reload your own information** 
accross SikuliX sessions or only across different runs of same or different scripts/programs.
Internally the item names get a unique prefix, to avoid name mangling (currently ``nonSikuli_``).

.. py:method:: Sikulix.prefStore(key, value)

	Store a key-value-pair in Javas persistent preferences store

	:param key: an item name as string
	:param value: a string value to be stored as the item's content

.. py:method:: Sikulix.prefLoad(key[, value])

	Retrieve the value of a previously stored key-value-pair using key as the item's name

	:param key: an item name as string
	:param value: an optional string value to be returned, if the item was not yet stored like some default
	:return: the item's content if the item exists, otherwise an empty string or the given default

.. py:method:: Sikulix.prefRemove(key)

	Permanently remove the key-value-pair using key as the item's name

	:param key: an item name as string
	:return: the item's content if the item exists, otherwise an empty string

.. py:method:: Sikulix.prefRemove()

	Permanently remove all key-value-pairs stored before using :py:meth:`Sikulix.prefStore`

	:param key: an item name as string
	:return: the item's content if the item exists, otherwise an empty string

Get Information about the runtime environment
---------------------------------------------

The ``class Env`` is deprecated and should not be used anymore. The contained features 
are moved to other places and redirected from inside ``class Env`` to be downward compatibel.

**NOTE:** In the following the **non-Env methods** are the replacements, 
that should be used instead. 

.. py:method:: Settings.getOS() 
	Env.getOS()
	Settings.getOSVersion
	Env.getOSVersion()
	
	Get the type ( ``getOS()`` ) and version string ( ``getOSVersion()`` ) 
	of the operating system your script is running on.
	
	An example using these methods on a Mac is shown below::

		# on a Mac
		myOS = Settings.getOS()
		myVer = Settings.getOSVersion()

		if myOS == OS.MAC:
			print "Mac " + myVer # e.g., Mac 10.6.3
		else:
			print "Sorry, not a Mac"

		myOS = Settings.getOS()
		if myOS == "MAC" or myOS.startswith("M"):
			print "Mac " + myVer # e.g., Mac 10.6.3
		else:
			print "Sorry, not a Mac"
		
There are convenience functions, 
to **check wether we are running on a specific system**:

.. py:method:: Settings.isWindows()
	Settings.isMac()
	Settings.isLinux()
	
	:return: True if we are running on this system, False otherwise

.. py:method:: Settings.getSikuliVersion()
	Env.getSikuliVersion()
	
	Get the version of Sikuli.

	:return: a string containing the version string
	
	::

		if not Settings.getSikuliVersion().contains("1.0.1"):
			print "This script needs SikuliX 1.0.1"
			exit(1)
		
.. py:method:: App.getClipboard()
	Env.getClipboard()

	Get the content of the clipboard if it is text, otherwise an empty string.

	**NOTE:** Be careful, when using ``Env.getClipboard()`` together with ``paste()``,
	since paste internally uses the clipboard to transfer text to other
	applications, the clipboard will contain what you just pasted. Therefore, if you
	need the content of the clipboard, you should call ``Env.getClipboard()`` before
	using ``paste()``.

	**Tipp**: When the clipboard content was copied from a web page that mixes images and
	text, you should be aware, that there may be whitespace characters around and
	inside your text, that you might not have expected. In this case, you can use
	``Env.getClipboard().strip()`` to at least get rid of surrounding white spaces.

.. py:method:: Key.isLockOn(key-constant)
	Env.isLockOn(keyConstant)

	Get the current status ( on / off ) off the respective key. Only one key can be specified.
	
	:param keyConstant: one of the key constants ``Key.CAPS_LOCK``, ``Key.NUM_LOCK``, ``Key.SCROLL_LOCK``
	:return: True if the specified key is on, False otherwise
	
	Further information about key constants can be found in Class :py:class:`Key`.


.. py:method:: Mouse.at()
	Env.getMouseLocation()

	Get the current location of the mouse cursor.
	
	:return: a :py:class:`Location` object of the position of the mouse cursor on the screen.
	

Advanced Settings for Speed and Robustness
------------------------------------------

**Note** **It is not recommended, to use this.** 

	With version 1.2 the matching process will be revised anyway and might bring other options.
	If you really want to speed up the search process, take care, 
	that you are searching in a region being as small as possible.
	
	:ref:`Here you get more information about how to make your workflow fast and robust <BestPractices>`.

.. py:method:: Vision.setParameter(param, value)

	Set the parameter *param* of the vision algorithm to *value*.
	
	:parameter param: a string that indicates the parameter to set.
	:parameter value: a float value.


.. py:method:: Vision.getParameter(param)

	Get the parameter *param* of the vision algorithm.
	
	:parameter param: a string that indicates the parameter to get.
	:return: the float value of the specified parameter.


.. _min-target-size:

**MinTargetSize**

``MinTargetSize`` is the minimum image size to which Sikuli can resize.

If you feel that Sikuli is running too slow, 
you might try a smaller value than 12. On the other hand, if you see Sikuli returns a match 
that is not what you expect, i.e. a false match, 
try to increase ``MinTargetSize`` to make Sikuli be more robust to small details.

You can tune this parameter using the following Jython code::

   from org.sikuli.natives import Vision
   Vision.setParameter("MinTargetSize", 6) # the default is 12.
   
Setting the size to a smaller value would make the matching algorithm be faster.
