/General features regarding scripting and image handling
=======================================================

.. _ControllingSikuliScriptsandtheirBehavior:

Controlling Sikuli Scripts and their Behavior
---------------------------------------------

.. py:function:: setShowActions(False | True)

	If set to *True*, when a script is run, Sikuli shows a visual effect (a blinking
	double lined red circle) on the spot where the action will take place before
	executing actions (e.g. ``click()``, ``dragDrop()``, ``type()``, etc) for about 2 seconds in the
	standard (see :py:attr:`Settings.SlowMotionDelay` ). The default setting is False.

.. py:function:: exit([value])

	Stops the script gracefully at this point. The value is returned to the calling
	environment. 

.. py:class:: Settings

.. py:attribute:: Settings.ActionLogs
	Settings.InfoLogs
	Settings.DebugLogs
	
  see the section about :ref:`debug and log messages <DebugLog>`.
	
.. py:attribute:: Settings.MinSimilarity

	The default minimum similiarty of find operations.
        While using a :py:meth:`Region.find` operation, 
        if only an image file is provided, Sikuli searches
        the region using a default minimum similarity of 0.7.

.. py:attribute:: Settings.MoveMouseDelay

	Control the time taken for mouse movement to a target location by setting this
	value to a decimal value (default 0.5). The unit is seconds.  Setting it to
	0 will switch off any animation (the mouse will "jump" to the target location). 

	As a standard behavior the time to move the mouse pointer from the current
	location to the target location given by mouse actions is 0.5 seconds. During
	this time, the mouse pointer is moved continuosly with decreasing speed to the
	target point. An additional benefit of this behavior is, that it gives the
	active application some time to react on the previous mouse action, since the
	e.g. click is simulated at the end of the mouse movement ::

		mmd = Settings.MoveMouseDelay # save default/actual value
		click(image1) # implicitly wait 0.5 seconds before click
		Settings.MoveMouseDelay = 3
		click(image2) # give app 3 seconds time before clicking again
		Settings.MoveMouseDelay = mmd # reset to original value

.. py:attribute:: Settings.DelayBeforeMouseDown
			Settings.DelayBeforeDrag
			Settings.DelayBeforeDrop

	*DelayBeforeMouseDown* specifies the waiting time before mouse down at the source
	location as a decimal value (seconds). 
	
	*DelayBeforeDrag* specifies the waiting time after mouse down at the source
	location as a decimal value (seconds). 
	
	*DelayBeforeDrop* specifies the
	waiting time before mouse up at the target location as a decimal value
	(seconds).

	**Usage**: When using :py:meth:`Region.dragDrop`, :py:meth:`Region.drag` and 
	:py:meth:`Region.dropAt` you may have situations, where the
	operation is not processed as expected. This may be due to the fact, that the
	Sikuli actions are too fast for the target application to react properly. With
	these settings the waiting time befor and after the mouse down at the source location and
	before the mouse up at the target location of a dragDrop operation  are
	controlled. The standard settings are 0.3 seconds for each value. The time that
	is taken, to move the mouse from source to target is controlled by
	:py:attr:`Settings.MoveMouseDelay` :: 

		Settings.DelayBeforeMouseDown = 0.5
		Settings.DelayBeforeDrag = 0.2
		Settings.DelayBeforeDrop = 0.2
		Settings.MoveMouseDelay = 3
		dragDrop(source_image, target_image)
		# time for complete dragDrop: about 4 seconds + search times
		
	**Be aware** The given values are only valid for the next following action. 
	The inner timing will be reset to the defaults after the action's completion.

.. py:attribute:: Settings.ClickDelay

	 Specify a delay between the mouse down and up in seconds as 0.nnn. This
	 only applies to the next click action and is then reset to 0 again. 
	 A value > 1 is cut to 1.0 (max delay of 1 second)

.. py:attribute:: Settings.TypeDelay

	 Specify a delay between the key presses in seconds as 0.nnn. This
	 only applies to the next type action and is then reset to 0 again. 
	 A value > 1 is cut to 1.0 (max delay of 1 second)

**NOTE:** If the internal timing of the compound mouse functions like 
``click()`` or ``dragDrop()`` is not suitable in your special situation, 
you might as well build your own functions using the basic mouse functions 
:py:meth:`Region.mouseDown`, :py:meth:`Region.mouseMove` and :py:meth:`Region.mouseUp`
	

.. py:attribute:: Settings.SlowMotionDelay

	Control the duration of the visual effect (seconds).


.. py:attribute:: Settings.WaitScanRate
			Settings.ObserveScanRate

	Specify the number of times actual search operations are performed per second
	while waiting for a pattern to appear or vanish.
	
	As a standard behavior Sikuli internally processes about 3 search operations per
	second, when processing a :py:meth:`Region.wait`, :py:meth:`Region.exists`,
	:py:meth:`Region.waitVanish`, :py:meth:`Region.observe`).  In cases where this
	leads to an excessive usage of system ressources or if you intentionally want to
	look for the visual object not so often, you may set the respective values to
	what you need. Since the value is used as a rate per second, specifying values
	between 1 and near zero, leads to scans every x seconds (e.g. specifying 0.5
	will lead to scans every 2 seconds)::

		def myHandler(e):
			print "it happened"
		# you may wish to save the actual settings before
		Settings.ObserveScanRate = 0.2
		onAppear(some_image, myHandler)
		observe(FOREVER, background = True)
		# the observer will look every 5 seconds ;-)

.. py:attribute:: Settings.ObserveMinChangedPixels

	 The minimum area size in pixels that changes it's content to trigger a change event when using :py:meth:`Region.onChange` 
	 when no value is specified. The default value is 50 (a rectangle of about 7x7 Pixels).

.. versionadded:: 1.1.3
.. py:attribute:: Settings.AlwaysResize

	A decimal value greater 0 and not equal to 1 to switch the feature on.
	
	With this setting you can tell SikuliX to generally resize all given images before a search operation using the given factor, which is applied to both width and height. The implementation internally uses the standard behavior of resizing a Java-AWT-BufferedImage.
	
	To switch the feature off again, just assign 0 or 1.
	
	**Usage** This might be helpful in cases, where you want to use a set of images in a different environment, where the rendering process shows everything in a different resolution. This option can only be used, if the behavior of the rendering is compatible to the above mentioned resize implementation.
	
	**Example** A working example situation is the scenario on a Mac with a Retina device, when you decide to capture your images with the standard Mac tools. In this case width and height of the captures will be doubled against a normal display (4 pixels for 1 pixel). Now you can say ``Settings.AlwaysResize = 0.5`` and every image will be downsized to the corerct pixel width and height as needed by the internal search operation. **Be aware** Using the SikuliX capture features will automatically adjust the images in the Retina situation already at the Java level. No need for extra work. 

	**Alternative 1** If you do not need this for all, but only for some images, then the class ``Pattern`` has a feature, to only scale the given image at time of search: :py:meth:`Pattern.resize`. 
	
	**Alternative 2** If you have the need for even more specific filtering and/or additional modifications of an image, you can use a global callback feature, that, if set, will allow you to return a modified version of the given image for the search: :py:attr:`Settings.ImageCallback`.
	
.. versionadded:: 1.1.3
.. py:attribute:: Settings.ImageCallback

	A callback function, that is visited before a search of an image. The callback itself is implemented at the Java level and hence can only use the SikuliX Java API and standard Java features.
	
	**Be aware:** :py:attr:`Settings.AlwaysResize` must be switched off and :py:meth:`Pattern.resize` must not be used for the image in parallel. 
	
	**Usage** ::
	
		    * Java example
		    // define and activate the callback
		    Settings.ImageCallback = new ImageCallback() {
		      public BufferedImage callback(Image img) {
		        BufferedImage bufferedImage = img.get();
			// add code, to modify the buffered image
			// and return the modified or original (=noop) BufferedImage
			return bufferedImage;
		      }
		    };
		    // deactivate the callback
		    Settings.ImageCallback = null;

		    * Jython example
		    # define the callback
		    import org.sikuli.script.ImageCallback as ImageCallback
		    class MyCallback(ImageCallback):
		      def callback(self, img):
		        bufferedImage = img.get()
			# add Jython-Java code, to modify the buffered image
			# and return the modified or original (=noop) BufferedImage
			return bufferedImage
		    # activate the callback
		    Settings.ImageCallback = MyCallback()
		    # deactivate the callback
		    Settings.ImageCallback = None
		    
	
.. _DebugLog:

.. versionadded:: 1.1.0
Writing and redirecting log and debug messages
----------------------------------------------

these are the relevant Settings for user logging showing defaults:
  (False = switched off, True = switched on)
  
* ``Settings.UserLogs = True`` (False: user log calls are ignored)
* ``Settings.UserLogPrefix = "user"`` (message prefix)
* ``Settings.UserLogTime = True``
* ``Debug.setUserLogFile("absolute-path-to-file")`` (no default)

to write a user log message:
  ``Debug.user("text with %placeholders", args …)``
     where text is a string according to the rules of Java String.format().

Information about Java String formatting `can be found here <http://docs.oracle.com/javase/7/docs/api/java/util/Formatter.html>`_ 
  (rather formal, look for tutorials in the net additionally if this is new for you)

the messages look so:
  ``[prefix optional-timestamp] message-text with filled in arg values``

Being in Jython scripting one might as well use this:
  ``Debug.user("some text with %placeholders" % (list-of-args …))``

the settings for Sikuli's logging with the defaults:
    (False = switched off (message type not created), True = switched on)

* ``Settings.ActionLogs = True`` (message prefix: [log])
* ``Settings.InfoLogs = True`` (message prefix: [info])
* ``Settings.DebugLogs = False`` (message prefix: [debug])
* ``Settings.LogTime = False``
* ``Debug.setLogFile("absolute-path-to-file")`` 
  to redirect the Sikuli messages to a file, no default

**Debug messages** Sikuli internally issues debug messages all over the place, to show, what it is doing. Creating debug messages is dependant on the current DEBUG_LEVEL value:

* if 0, no debug messages are shown
* if >0, debug messages having a level <= DEBUG_LEVEL are created

The initial DEBUG_LEVEL is 0 and can be set with 

* the Java command line parameter ``-Dsikuli.Debug=n`` or 
* the command line parameter ``-d n`` when using SikuliX jars or command scripts.

Currently a suitable DEBUG_LEVEL is 3, that shows enough valuable information about what is going on internally.

If you ever encounter problems, that might have to do with SikuliX's internal processing, switch on debug messaging with level 3.

To avoid tons of not needed messages, you might switch debugging on and off on the fly for only critical sections in your workflow:

* switch on: ``Debug.on(n)`` setting the DEBUG_LEVEL=n (recommended: 3)
* switch off: ``Debug.off()`` 

Debug messages look so:
  ``[DEBUG optional-timestamp] message-text with filled in arg values``

and can be produced with
  ``Debug.log(level, "text with %placeholders", args …)``
    *Recommendation*: use 1 as level, since this is not used internally by SikuliX and allows you to switch your private debug messaging on ``Debug.on(1)`` and off.
  
**Logging Callback** Currently only for Jython scripting, there is a **logging callback** feature, that redirects the log messages to a given function in your script, where you can finally process the message for example with your own looging concept.

A message, that is redirected to a callback is ignored by the SikuliX log processing.
  *TAKE CARE:* you should avoid lengthy processing in the callback, since your workflow will wait for the callback to return

This is a basic usage example, where the callback function gets all messages::

  # a wrapper class is needed for the callback function (name it as you want)
  class myLogger(): 
    # a callback function (name it as you want)
    # you might have more than one for specific handling of message groups
    def callback(self, message):
        print message
        
  # prepare log redirect
  Debug.setLogger(myLogger()) # sets the object containing the callback functions
  
  # redirect all logging messages
  Debug.setLoggerAll("callback") # the name of the callback function as string
  # from now on myLogger.callback will receive the messages
  
Selective log message processing (callback is the name of your specific callback function):

* ``Debug.setLoggerUser("callback") # redirect messages [user]``
* ``Debug.setLoggerInfo("callback") # redirect messages [info]``
* ``Debug.setLoggerAction("callback") # redirect messages [log]``
* ``Debug.setLoggerError("callback") # redirect messages [error]``
* ``Debug.setLoggerDebug("callback") # redirect messages [debug]``

You might suppress the creation of the message header for all messages, so you only get the message body:
  use ``Debug.setLoggerNoPrefix(myLogger())`` instead of the initial ``Debug.setLogger(myLogger())``
  
.. index:: file and path handling

.. _FileAndPathHandling:

.. versionadded:: 1.1.0
File and Path handling - convenience functions
--------------------------------------------------------------

*available for Jython scripting only in the moment*

In more complex scripting situations it is often necessary to deal with paths to files and folders. To make this a bit more convenient, the following functions are available 
(`look here for the underlying Python features <https://docs.python.org/2.7/library/os.path.html>`_).

.. py:function:: getBundlePath()
  
  returns the path to the current .sikuli folder without trailing separator.
    (see also :ref:`ImagePath <ImageSearchPath>`)
  
.. py:function:: getBundleFolder()
  
  same as :py:func:`getBundlePath` but with trailing separator to make it suitable for string concatenation.
  
.. py:function:: getParentPath()
  
  returns the path to the parent folder of the current .sikuli folder without trailing separator.
  
.. py:function:: getParentFolder()
  
  same as :py:func:`getParentPath` but with trailing separator to make it suitable for string concatenation.
  
.. py:function:: makePath(path1, path2, path3, ...)

  returns a path with the correct path seperators for the system running on by concatenating the given path elements from left to right (given as strings). There is no trailing path seperator.

.. py:function:: makeFolder(path1, path2, path3, ...)

  same as :py:func:`makePath` but trailing path seperator to make it suitable for string concatenation.
  
**NOTE** **makePath and makeFolder** on Windows the first path element can be specified as a drive letter "X:"

.. py:function:: unzip(fromFile, toFolder)

	A convenience function to unzip a zipped container to a folder (implemented using the Java builtin support for zip files). The ending of the file does not matter, the content of the file is examined to find out, whether it is a valid zip container. A zipped folder structure is preserved in the target folder. Relative paths would be resolved against the current working folder. This can for example be used, to unpack jar files.

	:param fromFile: a file with a zipped content given as path string
	:param toFolder: the folder where to place the unzipped content given as path string
	:return: True if it worked, False otherwise
	
**NOTE** The complementary feature ``zip(fromFolder, toFile)`` will follow soon.

**NOTE on Java usage**::

	import org.sikuli.basics.FileManager;
	FileManager.unzip(fromFile, toFolder);
 
.. index:: Image Path

.. _ImageSearchPath:

.. versionadded:: 1.1.0
Image Search Path - where SikuliX looks for image files
--------------------------------------------------------------------------

.. versionadded:: 2.0.5
**Topic is currently revised to reflect the new features of ImagePath**

SikuliX maintains a list of locations to search for images when they are not found in the current script folder (a.k.a. BundlePath). This list named ``ImagePath`` is maintained internally, but can be inspected and/or modified using the following functions.

**GENERAL ASPECTS:** 
	* as long as an image file has the ending .png, this might be omitted.
	* you might use subfolders as well, to form a relative path to an image file
	* an image path might point to a location inside a jar file (:py:func:`add jar <addJarImagePath>`)
	* an image path might point to a location on the Java classpath (:py:func:`add class folder <addClassImagePath>`)
	* an image path might point to a folder in the net, that is accessible via HTTP (:py:func:`add net folder <addHTTPImagePath>`)
	* SikuliX internally manages a cache for the imagefile content (standard 64 MB), where images are held in memory, thus avoiding a reload on subsequent references to the same image file.

.. index:: Bundle Path

**The bundle path** can be accessed and modified so:

.. note:: 
	The bundle path can only be on the (local) file system, not in a jar, nor in the net (access via HTTP). 
	If you need places in a jar or in the HTTP net, use the ImagePath features.

.. py:function:: setBundlePath(path-to-a-folder)

	Set the base path for searching images. SikuliX sets
	this automatically to the path of the folder where the running script file (.py/.rb) is stored. 
	Therefore, you should use this function only if you really know what you are doing.

	Additionally images are searched for in the image path, that is a global
	list of other places to look for images 
        and the bundle path being the first entry. 
        It is implicitly extended by script
	folders, that are imported 
        (see: :ref:`Reuse of Code and Images <ImportingSikuliScripts>`).
        
.. py:function:: getBundlePath()

	Get a string containing the absolute path to a folder containing your images
	used for finding images and which is set by SikuliX IDE automatically 
        to the script folder (.sikuli). 
        You may use this function for example, to package your private files 
        together with the script or 
        to access the image files in the bundle for other purposes. 
        Be aware of the :ref:`convenience functions to manipulate paths <FileAndPathHandling>`.

.. note::
	**Java usage:** Since there is no default BundlePath, when not running a script, like in the situation, when using the Java API in Java program or other situations with the direct use of Java aware scripting languages, you can use this feature to provide places, where you have stored your images.
	
Example::
  
	import org.sikuli.script.ImagePath;
	ImagePath.setBundlePath("path to your image folder");
	screen.find("image1"); 
	screen.find("imageset1/image2"); 
	//first find omits .png, second find uses a relative path with a subfolder

**Other places, where Sikuli should look for images**, can be added to the ``ImagePath``. 

When searching images, the ``ImagePath list`` is scanned in the order of the list. The first image file with a matching image file name is used.

**Use the following functions to work with this list.**

.. py:function:: getImagePath()

	Get a list of paths where Sikuli will search for images. ::
	
		imgPath = getImagePath() # get the list
		# to loop through
		for p in imgPath:
			print p

.. py:function:: addImagePath(a-new-path)

	Add a new folder path to the end of the current list (avoids double entries)
	  
	As a convenience you might use this function also to add a path to a HTTP net folder like so 
	*sikulix.com:* or *sikulix.com:somefolder/images* (see ``addHTTPImagePath``)
	 
.. py:function:: addHTTPImagePath(a-new-path)

	Add a new folder path to the end of the current list (avoids double entries)
	  
	*a-new-path* is a net url like *sikulix.com* 
	optionally with a folder structure attached like so: *sikulix.com/images* 
	(a leading *http://* or *https://* is optional, if omitted *http://* is assumed)
	
	The folder must be accessible via HTTP/HTTPS and must allow HTTP-HEAD requests on the contained image files 
	(this is checked at the time when trying to add the path entry).
	 
.. py:function:: removeImagePath(a-path-already-in-the-list)

	Remove the given path from the current list. Cached images loaded from that path are removed from the cache.

.. py:function:: resetImagePath(a-path)

	Clears the current list and sets the first entry to the given path (hence gets the BundlePath). The image cache is cleared completely..

.. note::
	Paths must be specified using the correct path separators (slash on Mac
	and Unix and double blackslashes on Windows). The convenience functions in :ref:`File and Path handling <FileAndPathHandling>` might be helpful.

.. note::
	This list is **automatically extended with script folders**, that are imported 
	(see: :ref:`Importing other Sikuli Scripts <ImportingSikuliScripts>`), 
	so their contained images can be accessed by only using their plain filenames. 

**Java usage: images in a jar**

It is possible to access images, that are stored inside of jar files. So you might develop a Java app, that comes bundled with the needed images in one jar file. 

To support the development cycle in IDE's, you might specify an alternate path, where the images can be found, when running inside the IDE. 

*Usage in Maven Projects:*

  Following the conventions of Maven projects you should store your images in a subfolder at ``src/main/resources`` for example ``src/main/resources/images``, which then at jar production will be copied to the root level of the jar. Not following this suggestion you have to work according to the case *other projects*.
  
  ``ImagePath.add("someClass/images")``
    where someClass is the name of a class contained in a jar or folder on the class path containing the images folder.
    
*Usage in other Projects:*
   
  ``ImagePath.add("someClass/images", alternatePath)``
      where *someClass* is the name of a class contained in a jar on the class path containing the given ``images`` folder at the root level of the jar.
      
      where *alternatePath* is a valid path specification, where the images are located, when running from inside an IDE.
      
*Example of a non-Maven project* where the images folder ``/imgs`` in this case is on the same level as the package folder ``testAPI`` containing the class file ``Test.java`` so both folders will be side by side at the root level of the runnable jar produced from this project::

	package testAPI;
	
	import org.sikuli.basics.Debug;
	import org.sikuli.script.ImagePath;
	import org.sikuli.script.Match;
	import org.sikuli.script.Screen;
	
	public class Test {
		public static void main(String[] args) {
			Screen s = new Screen();
			Debug.info("Screen: %s", s);
			String clazz = "testAPI.Test";
			String imgFolder = "/imgs";
			String img = "test.png";
			String inJarFolder = clazz + imgFolder;
			if (ImagePath.add(inJarFolder)) {
				Debug.info("Image Folder in jar at: %s", inJarFolder);
			} else {
				Debug.error("Image Folder in jar not possible: %s", inJarFolder);
			}
			Match target = s.exists(img);
			if (null == target) {
				Debug.error("Not found: ", img);
			} else {
				Debug.info("Found: %s at %s", img, target);
				s.hover();
			}
			Debug.info("... leaving");
		}
	}

**Be aware:** that you might use the Sikuli IDE, to maintain a script, that only contains the image filenames and then is used as image path in your Java app like ``ImagePath.add("myClass/myImages.sikuli")``, which e.g. in the Maven context will assume as image path ``src/main/ressources/myImages.sikuli``.

.. index:: import .sikuli

.. _ImportingSikuliScripts:

Importing other Sikuli Scripts (reuse code and images)
------------------------------------------------------

This is possible with SikuliX:

* import other .sikuli in a way that is compatible with Python module import (no module structures)
* import a python module structure including underlying Java classes from a jar-file, 
    that is dynamically loaded using the function :py:func:`load(jar-file) <load>`
* automatically access images contained in the imported .sikuli (no need to use
  :py:func:`setBundlePath`) 

**Note**: .skl cannot be imported. But you might unzip the .skl to a .sikuli, which then can be imported.

**The prerequisites**:

* the folders containing your .sikuli's you want to import have to
  be in ``sys.path`` (see below: Usage)

* Sikuli automatically finds other Sikuli scripts in the same directory, when they are imported

* your imported script **MUST** contain (recommendation: as first line) the
  following statement: 
    | ``from sikuli import *`` 
    | This is necessary for the Python environment to know the 
         Sikuli classes, methods, functions and global names

**Usage**:

* Add the path to the Sikuli module into ``sys.path`` 
    *not needed* for modules being in the same directory as the main script

Convenience function to add a path to sys.path:

.. versionadded:: 1.1.0
.. py:function:: addImportPath(path)

* Import your .sikuli using just its name. 
    For example, to import myModule.sikuli, just write ``import myModule``.

A basic example::

	# the path containing your stuff - choose your own naming
	# on Windows
	myScriptPath = "c:\\someDirectory\\myLibrary"
	# on Mac/Linux
	myScriptPath = "/someDirectory/myLibrary"

	# all systems (avoids double entries in sys.path)
	addImportPath(myScriptPath)

	# supposing there is a myLib.sikuli
	import myLib

	# supposing myLib.sikuli contains a function "def myFunction():"
	myLib.myFunction() # makes the call


**Note on contained images:** Together with the import, Sikuli internally uses
the feature :ref:`ImagePath <ImageSearchPath>` to make sure that images contained in imported .sikuli's are found automatically.

**Some comments on general rules for Python import**

*	An import is only processed once (the first time it is found in the program
	flow). So be aware: 
	
	*	If your imported script contains code outside of any function definitions ( ``def()`` ),
		this code is only processed once at the first time, when the import is evaluated

	*	Since the IDE does not reload the modules when running a script the next time, 
		you have to use the Jython's reload() function, 
		if you are changing imported scripts while they are in use:: 

			# instead of: import module
			import module
			reload(module) 

			# instead of: from module import *
			import module
			reload(module)
			from module import *  	
 	
*	Python has a so called namespace concept: names (variables, functions,
	classes) are only known in it's namespace: 
	
	*	your main script has it's own namespace

	*	Each imported script has its own namespace. So names contained in an 
		imported script have to be qualified with the module name (e.g. ``myLib.myFunction()`` )

	*	You may use ``from myLib import *``, which adds all names from myLib
		into your current namespace. So you can use ``myFunction()`` directly. When you
		decide to use this version, be sure you have a naming convention that
		prevents naming conflicts.

.. versionadded:: 1.1.1
The imports for other .sikuli scripts are now tracked during one IDE session. On rerun of a main script, the respective imports are automatically reloaded, so an extra reload() in these cases is no longer needed. 

.. versionadded:: 1.1.0

**Loading a jar-file containing Java/Python modules and additional resources as needed**

**BE AWARE currently only works up to Java 8** (in Java 9+ not yet possible)
	
.. py:function:: load(jar-file)

	Loads a jar-file and puts the absolute path to it into sys.path, so 
        the Java or Python code in that jar-file can be imported afterwards.
	
	:param jar-file: either a relative or absolute path to ``filename.jar``
	:return: ``True`` if the file was found, otherwise ``False``
	
.. py:function:: load(jar-file, image-folder)

	same as load(jar-file), but additionally adds the given folder to the image path. 
	image-folder is assumed to be a foldername available in the jar's rootlevel (not checked though).
	
	:param jar-file: either a relative or absolute path to ``filename.jar``
	:param image-folder: a relative path (always use / as path separator, no leading /)
	:return: ``True`` if the file was found, otherwise ``False``
	
**Search startegy** The given jar is searched as following (first match wins):
 - if given as absolute path it is checked for existence and processed (if not exists: no further action)
 - if given as relative path:
  - the current path (Jython: sys.path, Java: classpath)
  - the current folder (Jython only: bundle path)
  - the SikuliX Extensions folder
  - the SikuliX Lib folder
  
**Note for Java usage** at the Java level, this feature is available as ``Sikulix.load(jar [, folder])``
and adds the given jar to the end of the classpath on the fly. A given folder is added to the image path as mentioned above.

**Note on Python usage** more details and usage cases are discussed in :ref:`Using Python <UsingPython>`.
After a successful ``load()``, you might use the standard ``import something``, to make the module `something` available in your scripting context.

.. index:: run scripts

.. _RunningScripts:

.. versionadded:: 1.1.0

Running scripts and snippets from within other scripts and run scripts one after the other
------------------------------------------------------------------------------------------

What is meant by script and snippet?

 * **Script** means, that some code is stored somewhere in a file accessible in this context by giving it's relative or absolute filename or URL.
 * **Snippet** means some text stored in a string variable, that represents one or more lines of code in a denoted scripting language, for which an interpreter is available on the running system. 

You may call/run **scripts** from a script that is currently running, 
which saves the startup time for the called script 
and keeps available the original parameters given and the current image path.

.. py:function:: runScript(script_path, *parameter)
	
	Runs the script found at the given script-path handing over the given parameters in ``sys.argv[1+]``.
	The called script has it's own bundle path, but the current image path. On exit the bundle path of the 
	calling script is restored.
	
	:param: script_path: a path to a script folder (rules see below)
	:param: parameter: one or more parameters seperated by comma
	:return: the return code that the called script has given with exit(n) (exception: n = 1 - see note)

**Note on the returned value** If the called script runs into an exception (e.g. FindFailed), that is not catched
internally, the exception info is logged as [error] messages and the returned value is set to 1. So to sort things
out correctly, the called script should use exit(0) (which is the default if omitted) on success and use exit(n) with n > 1,
to signal other cases to the calling script. Do not use values < 0.

An example::

    exitValue = runScript(whatever)
    if exitValue > 1:
       print "there was a special case"
    elif exitValue == 1:
       print "there was an exception"
    else:
       print "ran with success"
    exit(exitValue)

**BE AWARE:** each parameter MUST be a simple string.
	
**Rules for the given script_path**

 * absolut path to a folder in the file system
 * relative path to a folder taken as relative to the working folder
 * the path spec can contain leading or intermediate ../
 * a path preceded by ./ means the same folder, that the calling script is located
 * a pointer to a folder in the HTTP net 
 * in any case .sikuli can be omitted
 * if it is a .skl, then it must be noted as script.skl
 
 **Special usage notes for scripts located in the net**
  - must be accessible via HTTP
  - the location specifier can be one of these:
   - ``base-url:folder/script``
   - ``http://base-url:folder/script``
   - ``http://base-url/folder/script``
   - where folder is optional and might have more than one level with / as separator
   - where script is the folder containing the script file (Python, Ruby or JavaScript) and the images (no .sikuli appended!)
  - The contained script file must have the same name as the script folder and a suffix:
   - for JavaScript ``.js``
   - for Python ``.py.txt``
   - for Ruby ``.rb.txt``
   - the additional suffixes ``.txt`` are currently necessary, to avoid download problems (will be addressed in version 2)
 
 This feature allows to create a main script, that contains a row of ``runScript() commands``, 
 thus running these scripts one after the other in the same context (no startup delay).
 Using the return codes and the parameters allows to create medium complex workflows
 based on smaller reuseable entities. 
 
 **Another option to run a series of scripts** without the startup delay for the second script and following 
 is to run from commandline using option -r (:ref:`see Running from command line <RunningScriptsFromCommandLine>`)
 
You may run **snippets** by simply issuing
 
.. py:function:: runScript(snippet)
 
 	currently available: 
 	
 	 * AppleScript on Mac (script type word: applescript)
 	 * PowerShell on Windows (script type word: powershell)
 	
 	For version 2 there will be a plugin system to easily add other scripting engines.

	:param: snippet: a string containing the scripting statements after the word identifying the script type 
	:return: the return code that was returned by the interpreter running this snippet
	
**Example for Applescript**:

	``returnCode = runScript('applescript\n tell application "Mail" to activate')``
	
	or like this for a multiline snippet::
	
	  cmd = """
	  applescript
	  tell application "Mail" to activate
	  display alert "Mail should be visible now"
	  """
	  returnCode = runScript(cmd)
	  
	
**Example for PowerShell**:

	``returnCode = runScript('powershell\n get-process')``
	
	or like this for a multiline snippet::
	
	  cmd = """
	  powershell
	  get-process
	  """
	  returnCode = runScript(cmd)
	  
If the snippet produces some output on stdout and/or stderror, this is accessible after return using:

	``commandOutput = RunTime.get().getLastCommandResult()``
	
where the error output comes after a line containing ``***** error *****``
