General Settings and Access to Environment Information
======================================================

**Sikuli Level**

Sikuli internally uses the class :py:class:`Settings` to store globally used
settings. Publicly available attributes may be accessed by using
``Settings.[name-of-an-attribute]`` to get it's value and ``Settings.attribute = value``
to set it. It is highly recommended to only modify attributes, that are described in
this document or when you really know, what you are doing.

Actually all attributes of some value for scripting are described in the 
topic :ref:`Controlling Sikuli Scripts and their Behavior <ControllingSikuliScriptsandtheirBehavior>`.

**Jython/Python Level**

You may use all settings, that are defined in standard Python/Jython and that are
available in your system environment. The modules sys and time are already imported,
so you can use their methods without the need for an import statement.

``sys.path`` may be one of the most valuable settings, since it is used by
Python/Jython to locate modules, that are referenced using ``import module``. It is
a list of path's, that is e.g. maintained by Sikuli to implement :ref:`Importing
other Sikuli Scripts <ImportingSikuliScripts>` as a standard
compliant feature.

If you want to use ``sys.path``, it is recommended to do it as shown in the following
example, to avoid appending the same entry again::

	myPath = "some-absolute-path"
	if not myPath in sys.path:
		sys.path.append(myPath)

**Java Level**

Java maintains a global storage for settings (key/value pairs), that can be accessed
by the program/script. Sikuli uses it too for some of it's settings. Normally it is
not necessary to access these settings at the Java level from a Sikuli script, since
Sikuli provides getter and setter methods for accessing values, that make sense for
scripting. One example is the list of paths, that Sikuli maintains to specify
additional places to search for images (please refer to :ref:`Importing
other Sikuli Scripts <ImportingSikuliScripts>` for more information).

If needed, you may access the java settings storage as shown in the following
example::

	import java
	
	# get a value
	val = java.lang.System.getProperty("key-of-property")
	
	# set a property's value
	java.lang.System.getProperty("key-of-property", value)

**Other Environment Information**

.. py:method:: Env.getOS()
		Env.getOSVersion()
		
	Get the type ( ``getOS()`` ) and version ( ``getOSVersion()`` ) of the operating system your
	script is running on.
	
	An example using these methods on a Mac is shown below::

		# on a Mac
		myOS = Env.getOS()
		myVer = Env.getOSVersion()

		if myOS == OS.MAC:
			print "Mac " + myVer # e.g., Mac 10.6.3
		else:
			print "Sorry, not a Mac"

		myOS = str(Env.getOS()) 
		if myOS == "MAC" or myOS.startswith("M"):
			print "Mac " + myVer # e.g., Mac 10.6.3
		else:
			print "Sorry, not a Mac"

.. versionadded:: X1.0-rc2
.. py:method:: Env.getSikuliVersion()
		
	Get the version of Sikuli.
	
	:return: a string containing the version text of the IDE window title without "Sikuli "
	
	An example for Sikuli X-1.0rc2::

		if not Env.getSikuliVersion() == "X-1.0rc2":
			print "This script needs Sikuli X-1.0rc2"
			exit(1)
	
.. py:method:: Env.getClipboard()

	Get the content of the clipboard if it is text, otherwise an empty string.

	*Note*: Be careful, when using ``Env.getClipboard()`` together with ``paste()``,
	since paste internally uses the clipboard to transfer text to other
	applications, the clipboard will contain what you just pasted. Therefore, if you
	need the content of the clipboard, you should call ``Env.getClipboard()`` before
	using ``paste()``.

	*Tip*: When the clipboard content was copied from a web page that mixes images and
	text, you should be aware, that there may be whitespace characters around and
	inside your text, that you did not expect. In this case, you can use
	``Env.getClipboard().strip()`` to get rid of surrounding white spaces.

.. versionadded:: X1.0-rc2
.. py:method:: Env.isLockOn(key-constant)

	Get the current status ( on / off ) off the respective key. Only one key can be specified.
	
	:parameter key-constant: one of the key constants ``Key.CAPS_LOCK``, ``Key.NUM_LOCK``, ``Key.SCROLL_LOCK``
	:return: True if the specified key is on, False otherwise
	
	Further information about key constants can be found in Class :py:class:`Key`.


.. versionadded:: X1.0-rc2
.. py:method:: Env.getMouseLocation()

	Get the current location of the mouse cursor.
	
	:return: a :py:class:`Location` object of the position of the mouse cursor on the screen.
	


Advanced Settings for Speed and Robustness
------------------------------------------


.. py:method:: Vision.setParameter(param, value)

	Set the parameter *param* of the vision algorithm to *value*.
	
	:parameter param: a string that indicates the parameter to set.
	:parameter value: a float value.


.. py:method:: Vision.getParameter(param)

	Get the parameter *param* of the vision algorithm.
	
	:parameter param: a string that indicates the parameter to get.
	:return: the float value of the specified parameter.



The available parameters for tuning the vision algorithm of Sikuli is listed as
follows.

.. _min-target-size:

MinTargetSize
^^^^^^^^^^^^^

``MinTargetSize`` is the minimum image size to which Sikuli can resize.

**Note** It is not recommended, to use this. 
With version 1.2 the matching process will be revised anyway and might bring other options.
If you really want to speed up the search process, take care, that you are searching in a region being as small as possible.


If you feel that Sikuli is running too slow, 
you might try a smaller value than 12. On the other hand, if you see Sikuli returns a match 
that is not what you expect, i.e. a false match, 
try to increase ``MinTargetSize`` to make Sikuli be more robust to small details.

You can tune this parameter using the following Jython code::

   from org.sikuli.natives import Vision
   Vision.setParameter("MinTargetSize", 6) # the default is 12.
   
Setting the size to a smaller value would make the matching algorithm be faster.
