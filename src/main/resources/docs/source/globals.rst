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

Store some of your information persistently and reload it later
---------------------------------------------------------------

You can have a so called **Property File** somewhere on the file system, that you can prefill with key-value-pairs representing information, that can be used by your scripts for whatever purpose at runtime. So it can be used instaed of commandline parameters, for some kind of data-driven approach or for any other solution, that needs information to be persistent over time.

::

		# this is a property file
		key1 = value1
		key2 = value2
		...
		
At runtime in your script, you first load such a property file into an in-memory store and then access the values using their keys (both basically are strings). You might change existing values, add new values and remove values. At any time you might save the store content back to the originating file or to another file. 

Currently there is no Auto-Save feature, so that your changes are lost in case of crashes before you saved the store back to a file. The feature might not be fully thread safe.

**Features that operate on the store as entity**

.. py:method:: loadOpts(filePath)

	load a property file into an internal store
	
	:param filePath: absolute or relative to the working folder
	:return: the reference to the internal store to be used with the store functions

.. py:method:: saveOpts(store)

	save the store back to the file it was loaded from
	
	:param store: the reference to a loaded store
	:return: true if it worked, false otherwise

.. py:method:: saveOpts(store, filepath)

	save the store to the given file, overwritten without notice
	
	:param store: the reference to a loaded store
	:param filePath: absolute or relative to the working folder
	:return: true if it worked, false otherwise

.. py:method:: makeOpts()

	make a new, empty internal store, that might be saved to a file later
	
	:return: the reference to the internal store to be used with the store functions

.. py:method:: delOpts(store)

	purge all key-value-pairs from the store (make it empty)
	
	:param store: the reference to a loaded store
	:return: true if it worked, false otherwise

.. py:method:: hasOpts(store)

	count the key-value-pairs in the store
	
	:param store: the reference to a loaded store
	:return: a positive number (0 means empty)
	
.. py:method:: getOpts(store)

	load the key-value-pairs into a dictionary (Java: Map(String, String)), to be able to use more powerful features on the store information
	
	:param store: the reference to a loaded store
	:return: the dictionary filled with the key-value-pairs
	
.. py:method:: setOpts(store, map)

	store the key-value-pairs from a dictionary (Java: Map(String, String)) to the given store
	
	:param store: the reference to a loaded store
	:param map: the dictionary/map containing the key-value-pairs
	:return: the number of stored key-value-pairs (0 might signal a problem)
	
**Features that operate on individual entries in a loaded store**

.. py:method:: hasOpt(store, key)

	check the existence of a key-value-pair
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:return: true if the key exists, false otherwise

.. py:method:: getOpt(store, key[, default])

	read the value of a specific key and get the default value, if the key does not exist. If the key does not exists and no default is given, an empty string is returned.
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:param default: an optional value in case the key does not exist in the store
	:return: the stored value or the default
	
.. py:method:: setOpt(store, key, value)

	set the value of a specific key. if the key does not exist, the key-value-pair is added, otherwise the value is overwritten.
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:param value: a string value to be stored with the given key
	:return: the stored value before the change, an empty string if the key did not exist yet
	
.. py:method:: delOpt(store, key)

	delete the key-value-pair from the store
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:return: the stored value before the deletion, an empty string if the key did not exist yet
	
**Convenience functions for number values**

Since the values in the store are strings only, the following functions take care for the necessary conversions. All returned numbers are of format double.

.. py:method:: getOptNum(store, key[, default])

	read the value of a specific key and get the default value, if the key does not exist. If the key does not exists and no default is given, a 0.0 is returned.
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:param default: an optional number value in case the key does not exist in the store
	:return: the stored value as double or the default
	
.. py:method:: setOptNum(store, key, value)

	set the value of a specific key. if the key does not exist, the key-value-pair is added, otherwise the value is overwritten.
	
	:param store: the reference to a loaded store
	:param key: the key as string of a stored key-value-pair
	:param value: an valid number value to be stored with the given key
	:return: the stored value before the change, a 0.0 if the key did not exist yet
	
Using SikuliX's preferences store for your own key-value pairs (deprecated version)
-----------------------------------------------------------------------------------

.. note::

	The following functions are deprecated since 2.0.6 - use the ones below instead for new implementations
	
.. note::

	The following feature only works on the same machine

	... and has nothing to do with the above feature, but can of course be combined.

	You might use SikuliX's persistent storage, to **store and reload your own information** 
	accross SikuliX sessions or only across different runs of same or different scripts/programs.

	There is no feature to preload the store before the first run nor to export your information.

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

	:return: a dictionary with the key-value pairs removed (might be empty)
	

Using SikuliX's preferences store for your own key-value pairs (new version)
----------------------------------------------------------------------------

.. versionadded:: 2.0.6
	
.. note::

	The following features (except export/import) only works on the same machine

	... and has nothing to do with the Options feature, but can of course be combined.

	You might use SikuliX's persistent storage, to **store and reload your own information** 
	accross SikuliX sessions or only across different runs of same or different scripts/programs.

	Additionally you might store/reload the complete set to use it on other machines or even share it (export/import as a special XML file).

.. py:method:: prefStore(key, value)

	Store a key-value-pair in SikuliX's persistent preferences store

	:param key: an item name as string
	:param value: a string value to be stored as the item's content

.. py:method:: prefLoad(key[, default])

	Retrieve the value of a previously stored key-value-pair using key as the item's name

	:param key: an item name as string
	:param default: an optional string value to be returned, if the item was not yet stored (as default)
	:return: the item's content if the item exists, otherwise an empty string or the given default

.. py:method:: prefAll()

	Get all key-value-pairs stored before using :py:meth:`prefStore` as a dictionary

	:return: a dictionary with the key-value pairs (might be empty)

.. py:method:: prefRemove(key)

	Permanently remove the key-value-pair using key as the item's name

	:param key: an item name as string
	:return: the item's content if the item exists, otherwise an empty string

.. py:method:: prefRemove()

	Permanently remove all key-value-pairs stored before using :py:meth:`prefStore`

	:return: a dictionary with the key-value pairs removed (might be empty)
	
.. note::

	The before mentioned features internally store the given value with the given key preceeded with a prefix USER_, 
	to avoid a mixup with SikuliX related options. 
	Hence the XML file created with export will have the keys with this prefix (relevant only when working directly with the XML file).

.. py:method:: prefExport(path)

	Store SikuliX's preference store completely in an XML file with the given path-file specification. 

	:return: True (= success) or False
	
.. py:method:: prefImport(path)

	Load SikuliX's preference store completely from an XML file with the given path-file specification, 
	that was created before by :py:meth:`prefExport`

	:return: True (= success) or False
	
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

.. py:method:: Settings.getVersion()
	Env.getSikuliVersion()
	
	Get the version of Sikuli.

	:return: a string containing the version string
	
	::

		if not Settings.getSikuliVersion().contains("1.0.1"):
			print "This script needs SikuliX 1.0.1"
			exit(1)
		
.. py:method:: Settings.getVersionBuild()
	Env.getSikuliVersionBuild()

	Get the version of Sikuli with detaile build info (number and date-time)

	:return: a string containing the version string

	::

		print Settings.getVersionBuild()
		# prints: 1.1.4-SNAPSHOT-#205-2019-02-22_10:50

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
	
