The Application Class (App)
===========================

.. py:class:: App

.. versionadded:: 2.0.0

**Completely revised - makes sense to read**

Using class or instance methods
-------------------------------

Generally you have the choice between using the class methods (e.g.
``App.open("application-identifier")``) or first create an App instance and use
the instance methods afterwards (e.g. ``myApp = App("application-identifier")``
and then later on ``myApp.open()``). There is no recomendation for a preferred usage, but
if you plan to act on the same app or window more often, 
it might be more efficient, to use the instance approach. 

For the string ``application-identifier`` principally the same rules apply, as if you would use the identifier
on a command line. Especially if path specification contains blanks,
it must be enclosed in double-quotes.

If the application name is given without a path specification, then it must be found on the system,
according to the rules of that system (Windows: is on system path, Mac: is in an /Applications folder, ...).

Examples::
	
	# Python scripting
	myApp = App("someApp")
	myApp = App("/myapps/someApp.app")
	myApp = App(r"c:\myapps\someApp.exe")
	myApp = App(r'"c:\folder with spaces\someApp.exe"')
	myApp = App('"c:\\folder with spaces\\someApp.exe"')
	# in Java with spaces
	myApp = App("\"c:\\folder with spaces\\someApp.exe\"")	
	
.. _CreateAppInstance:

**How to create an App instance**

The basic choice is to just say ``someApp = App("some-app-identifier")`` and you
have your app instance, that you can later use together with its methods,
without having to specify the string again. 

Generally all class methods return an app instance, 
that you might save in a variable to use it later in your script.

At time of instance creation the process list is scanned for the name of the
executable using the given text. If this is found, the app instance is initialized with the respective
information (PID, executable, window title of main/frontmost window).
So you could directly ask the app instance, wether it is running (``isRunning()``), 
has a main window (``hasWindow()``), get the title of that main window (``getWindow()``) 
and get the process id (PID) (``getPID``).

The string representation of an app instance looks like this:
   ``[nPID:executableName (main/frontmost window title)] given text``

**BE AWARE** The ``application-identifier`` can no longer be (part of) a window title - it must specify an executable.
If you want to reference a running application with (part of) a window title, you have to use ``App.focus()``.

**NOTE** Currently the information, wether a window is hidden or minimized, is not available
and it is not possible yet, to bring such a window to front with a compound SikuliX feature.

**Open, close an application or focus on it**

.. _ClassAppMethods:

.. py:class:: App
  
	.. py:classmethod:: App(application)
	
		*Usage:* ``someApp = App(application)``

		Create an App instance, to later use with the instance methods (:ref:`see above <CreateAppInstance>`)

		The string ``application`` must allow the system to locate the application in the system specific mannor.
		If this is not possible you might try the full path to an application's executable.

		Optionally you might add parameters, that will be given to the application at time of open.

		There are 3 options:
		 - put the application string in apostrophes and the rest following the second apostroph will be taken as parameter string
		 - put `` -- `` (space 2 hyphens! space) between the applications name or path (no apostrophes!) and the parameter string.
		 - use :py:func:`setUsing` with an already existing application object (:ref:`created before <CreateAppInstance>`)

		:param application: The name of an application (case-insensitive) or the path to the executable and optionally parameters
		:return: an App object, that can be used with the instance methods
		
	.. py:classmethod:: open(application)
	
		*Usage:* ``App.open(application)``

		Open the specified application, if it is not yet opened and bring it to front

		:param application: the same as for ``App(application``
		:return: an App object, that can be used with the instance methods
		
		This method is functionally equivalent to :py:func:`openApp`.

	.. py:classmethod:: focus(title [, index])

		*Usage:* ``App.focus(title [, index])``

		Switch the input focus to a running application having a front-most window with a matching title.

		:param title: The name of an application (case-insensitive) or (part of) a window title (case-sensitive).
		:param index: optional number (counting from 0) telling which one you want to have in the row of possible matches.
		:return: an App object, that can be used with the instance methods

		If no index is given, it is taken as 0. If the index is not applicable (not enough matches)
		the returned App object is invalid and not useable.

		So you might loop through possible matches by counting the index up from 0 until you get an invalid

		If you specify the exact window title of an open window, you will get exactly
		this one. But if you specify some text, that is found in more than one open
		window title, you will get the first in the row of all these windows.
		So if you want exactly one specific window, you either
		need to know the exact window title or at least some part of the title text,
		that makes this window unique in the current context (e.g. save a document with
		a specific name, before accessing it's window).

		**macOS and Linux** not yet possible, to identify a running app by part of the title of it's frontmost window.
		If you want to use a window title to match the application, use ``App.focus()`` before,
		to get a valid App object.

		This method is functionally equivalent to :py:func:`switchApp`.

	.. py:classmethod:: close(application)
	
		*Usage:* ``App.close(application)``

		It closes the running application matching the given string. It does nothing
		if no running application matches. If you want to use a window title to match the application, use ``App.focus()`` before,
		to get a valid App object.

		:param application: The name of an application (case-insensitive)

		This method is functionally equivalent to :py:func:`closeApp`. 

	.. py:method:: setUsing(parametertext)

		*Usage:* ``appName = someApp.setUsing("parm1 x parm2 y parm3 z")``
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		:param parametertext: a string, that is given to the application at startup (when using ``open()`` ) as if you would start the app from a commandline.

	.. py:method:: setWorkDir([workingFolder])

    **WINDOWS ONLY**
		*Usage:* ``appName = someApp.setWorkDir("path to the working directory")``
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		:param workingFolder: the absolute path, which gets the working directory. The location of the app exec-file is used if omitted.

	.. py:method:: open([waitTime])

		*Usage:* ``someApp.open()``
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Open this application.

		:param waitTime: optional: seconds as integer, that should be waited for the app to get running
		:return: the app instance, which will be invalid, if open failed.

	.. py:method:: focus()

		*Usage:* ``someApp.focus()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Switch the input focus to this application.

		:return: the app instance, which will be invalid, if open failed.

	.. py:method:: close([waitTime])

		*Usage:* ``someApp.close()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Close this application.

		:param waitTime: optional: seconds as integer, that should be waited for the app to no longer being running

		:return: the app instance, which should be invalid (not running) afterwards.

	.. py:method:: closeByKey([waitTime])

		*Usage:* ``someApp.closeByKey()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Close this application by bringing it to front first (``focus()``) an then issue the systemspecific
		keybord shortcut to close an application. This might help in situations where the normal ``close()`` leads to
		oddities at a later restart of the application.

		:param waitTime: optional: seconds as integer, that should be waited for the app to no longer being running

**Getting information about a running application**

	.. py:method:: isValid()

		*Usage:* ``if not someApp.isValid(): someApp.open()``
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		:return: True if the app has a process ID, False otherwise

		**Be aware** This simply checks wether the app object has a valid process ID. Hence it returns instantly. But there
		is no guarantee, that the app is still running. If you want to be sure, you have to use ``isRunning(0)``, which
		evaluates the state of the app, but might take some 100 millisecs, depending on your system's state.

	.. py:method:: isRunning([waitTime])
	
		*Usage:* ``if not someApp.isRunning(): someApp.open()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		:param waitTime: optional: seconds as integer, that should be waited for the app to get running
		:return: True if the app is running (has a process ID), False otherwise

		**Be aware** Until the wait time is elapsed, the state of the application is checked every second.
		If you use just ``isRunning()``, the state check is done twice, waiting 1 second in between.
		Hence this might take up to 2 seconds.
		If you want, that only one check is done, use ``isRunning(0)``.

	.. py:method:: hasWindow()
	
		*Usage:* ``if not someApp.hasWindow(): openNewWindow() # some private function``
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: True if the app is running and has a main window registered, False otherwise
		
	.. py:method:: getTitle()
	
		*Usage:* ``title = someApp.getTitle()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the title of the frontmost window of this application, might be an empty string
		
	.. py:method:: window()
	
		*Usage:* ``windowRegion = someApp.window()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the region of the frontmost window of this application
		
	.. py:method:: getPID()
	
		*Usage:* ``pid = someApp.getPID()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the process ID as number if app is running, -1 otherwise
		
	.. py:method:: getName()
	
		*Usage:* ``appName = someApp.getName()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the short name of the app as it is shown in the process list

		.. py:classmethod:: pause(waitTime)

		*Usage:* ``App.pause(someTime)`` (convenience function)

		Just do nothing for the given amount of time in seconds (integer or float).

Dealing with Application windows
--------------------------------

	.. py:classmethod:: focusedWindow()

		*Usage:* ``App.focusedWindow()``

		Identify the currently focused or the frontmost window and switch to it.
		Sikuli does not tell you, to which application this window belongs.

		:return: a :py:class:`Region` object representing the window or *None* if
			there is no such window.

		On Mac, when starting a script, Sikuli hides its window and starts
		processing the script. In this moment, no window has focus. Thus, it is
		necessary to first click somewhere or use ``App.focus()`` to focus on a
		window. In this case, this method may return *None*.

		On Windows, this method always returns a region. When there is no window
		opened on the desktop, the region may refer to a special window such as the
		task bar or an icon in the system tray.
	
		Example::

			# highlight the currently fontmost window for 2 seconds
			App.focusedWindow().highlight(2)

			# save the windows region before
			firstWindow = App.focusedWindow()
			firstWindow.highlight(2)

	.. py:method:: window([n])

		*Usage 1:* ``App(application).window([n])`` an App instance is created on the fly.
		
		*Usage 2:* ``someApp.window([n])`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Get the region corresponding to the n-th window of this application (Mac) or
		a series of windows with the matching title (Windows/Linux). 

		:param n: 0 or a positive integer number. If ommitted, 0 is taken as
			default.

		:return: the region on the screen occupied by the window, if such window
			exists and *None* if otherwise.
	
		Below is an example that tries to open a Firefox browser window and switches
		to the address field (Windows)::	

			# using an existing window if possible
			myApp = App("Firefox")
			if not myApp.window(): # no window(0) - Firefox not open
				App.open("c:\\Program Files\\Mozilla Firefox\\Firefox.exe")
				wait(2)
			myApp.focus()
			wait(1)
			type("l", KEY_CTRL) # switch to address field

		Afterwards, it focuses on the Firefox application, uses the ``window()`` method to
		obtain the region of the frontmost window, applies some operations
		within the region, and finally closes the window::

			# using a new window
			firefox = App.open("c:\\Program Files\\Mozilla Firefox\\Firefox.exe");
			wait(2)
			firefox.focus()
			wait(1)
			# now your just opened new window should be the frontmost 
			with Region(firefox.window()): # see the general notes below
				# some actions inside the window(0)'s region
				click("somebutton.png")
			firefox.close() # close the window - stop the process


		Below is another example that highlights all the windows of an
		application by looping through them (Mac)::

			# not more than 100 windows should be open ;-)
			myApp = App("Safari")
			for n in range(100):
				w = myApp.window(n)
				if not w: break # no more windows
				w.highlight(2) # window highlighted for 2 second


General aspects, hints and tipps
--------------------------------

		*	Be aware, that especially the window handling feature is experimental
			and under further development.

		*	Especially on Windows be aware, that there might be many matching
			windows and windows, that might not be visible at all. Currently the
			``window()`` function has no feature to identify a special window besides
			returning the region. So you might need some additional checks to be
			sure you are acting on the right window. 

		*	Windows/Linux: The ``close()`` function currently kills the application,
			without closing it's windows before. This is an abnormal termination and
			might be recognized by your application at the next start (e.g. Firefox
			usually tries to reload the pages).

		*	Even if the windows are hidden/minimized, their region that they have in
			the visible state is returned. Currently there is no Sikuli feature, to
			decide wether the given window(n) is visible or not or if it is
			currently the frontmost window. The only guarentee: ``window()``/``window(0)``
			is the topmost window of an application (Mac) or a series of matching
			windows (Windows/Linux). 

		*	Currently there are no methods available to act on such a window
			(resize, bring to front, get the window title, ...).

		Some tips:

		*	Check the position of a window's returned region: some apps hide there
			windows by giving them "outside" coordinates (e.g. negative) 

		*	Check the size of a window's returned region: normally your app windows
			will occupy major parts of the screen, so a window's returned region of
			e.g. 150x30 might be some invisible stuff or an overlay on the real app
			window (e.g. the "search in history" input field on the Safari Top-Sites
			page, which is reported as ``windows(0)``)

		*	If you have more than one application window, try to position them at
			different coordinates, so you can decide which one you act on in the
			moment.

		*	It is sometimes possible to use the OCR text extraction feature 
			:py:meth:`Region.text` to obtain the window title.

Some technical information on the implementation
------------------------------------------------

The following information on how the features are implemented might help to understand problematic situations or
to make suggestions on enhancements.

**Windows**

 - Applications are opened by internally running an appropriate ``start command``.

 - The state of an application is evaluated by using the appropriate output of a ``tasklist command``.

 - ``focus()`` and the ``window related`` features are still implemented at the native level (``WinUtil.dll``). Same goes
   for finding an application by part of its frontmost window's title.

**Mac**

 - Applications are opened by internally running an appropriate ``open command``.

 - The state of an application is evaluated by using the output of an appropriate AppleScript snippet, internally run
   using macOS's ``osascript command``.

 - ``focus()`` and the ``window related`` features are still implemented at the native level (``MacUtil.dll``).

**Linux**

The features are implemented by either running an appropriate shell command or by using appropriate features of
the packages ``xdotool`` and ``wmctrl``, which must be provided by the user.


