The Application Class (App)
===========================

.. py:class:: App

Using class or instance methods
-------------------------------

Generally you have the choice between using the class methods (e.g.
``App.open("application-identifier")``) or first create an App instance and use
the instance methods afterwards (e.g. ``myApp = App("application-identifier")``
and then later on ``myApp.open()``). There is no recomendation for a preferred usage, but
if you plan to act on the same app or window more often, 
it might be more efficient, to use the instance approach. 

For the string ``application-identifier`` principally the same rules apply, as if you would use the identifier on a command line. Especially if an item (app-name, app-path or window-name) contains blanks, it must be enclosed in double-quotes.

If the application name is given without a path specification, then it must be found on the system, according to the rules of that system (Windows: is on system path, Mac: is in an /Applications folder, ...).

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

(Windows/Linux) If it is not found, the titles of the currently 
known windows are scanned, wether they contain the given text.
The first matching window found evaluates to the application, that initializes the app instance.

If neither the executable is found in the process list, nor a matching window, 
the app instance is initialized as not running (the PID is set to -1). The given text is remembered.

If you specify the exact window title of an open window, you will get exactly
this one. But if you specify some text, that is found in more than one open
window title, you will get the first in the row of all these windows. 
So if you want exactly one specific window, you either
need to know the exact window title or at least some part of the title text,
that makes this window unique in the current context (e.g. save a document with
a specific name, before accessing it's window).

(Mac OS X) not yet possible, to identify a running app by part of the title of one of it's windows.
The window title you get by ``getWindow()`` is the one of the currently frontmost window of that application.

**NOTE** Currently the information, wether a window is hidden or minimized, is not available 
and it is not possible yet, to bring such a window to front with a compound SikuliX feature.

**Open, close an application or focus on it**

 

.. _ClassAppMethods:

.. py:class:: App
  
	.. py:classmethod:: App(application)
	
		*Usage:* ``someApp = App(application)``

		Create an App instance, to later use with the instance methods (:ref:`see above <CreateAppInstance>`)

		:param application: The name of an application (case-insensitive), that can be found in the path used by the system to locate applications, or the full path to an application. Optionally you might add parameters, that will be given to the application at open (see :py:func:`setUsing`).	
		:return: an App object, that can be used with the instance methods
		
	.. py:classmethod:: open(application)
	
		*Usage:* ``App.open(application)``

		Open the specified application, if it is not yet opened and bring it to front

		:param application: The name of an application (case-insensitive), that can be found in the path used by the system to locate applications, or the full path to an application (Windows: use double backslash \\ in the	path string to represent a backslash)
			
		:return: an App object, that can be used with the instance methods, None in case of failing
		
		This method is functionally equivalent to :py:func:`openApp`.

	.. py:method:: open([waitTime])
	
		*Usage:* ``someApp.open()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		Open this application.

		:param waitTime: optional: seconds as integer, that should be waited for the app to get running
		:return: the app instance or null/None if open failed

	.. py:classmethod:: focus(application)

		*Usage:* ``App.focus(application)``

		Switch the input focus to an application/window.

		:param application: The name of an application (case-insensitive) or (part of) a window title (Windows/Linux) (case-sensitive).

		:return: an App object, that can be used with the instance methods, , None in case of failing
		
	.. py:method:: focus()
	
		*Usage:* ``someApp.focus()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Switch the input focus to this application/window.


	.. py:classmethod:: close(application)
	
		*Usage:* ``App.close(application)``

		It closes the
		given application or the matching windows (Windows/Linux). It does nothing
		if no running application or opened window (Windows/Linux) can be
		found. On Windows/Linux, whether the application itself is closed depends on
		weather all open windows are closed or a main window of the application is
		closed, that in turn closes all other opened windows. 

		:param application: The name of an application (case-insensitive) or (part
			of) a window title (Windows/Linux)(case-sensitive).

		This method is functionally equivalent to :py:func:`closeApp`. 

	.. py:method:: close()

		*Usage:* ``someApp.close()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Close this application.
		
**Getting information about a running application**

	.. py:classmethod:: pause(waitTime)
	
		*Usage:* ``App.pause(someTime)`` (convenience function)
		
		Just do nothing for the given amount of time in seconds (integer or float). 


	.. py:method:: isRunning([waitTime])
	
		*Usage:* ``if not someApp.isRunning(): someApp.open()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:param waitTime: optional: seconds as integer, that should be waited for the app to get running
		:return: True if the app is running (has a process ID), False otherwise
		
		**Windows** It is common, to identify an app by (part of) it's window title. 
		If it is not open yet, one has to use methods, to open it first before proceeding.
		
		So this is a typical example, how to deal with that:
		
		Example::
			
			# we want to act in a VirtualBox VM window and use the VM's name
			# which is always part of the Window title when running
			vb = App("VM-name")
			if not vb.isRunning():
  				App.open(r'"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" startvm VM-name')
  				while not vb.isRunning():
    					wait(1) 
			vb.focus()
			appWindow = App.focusedWindow()
		
	.. py:method:: hasWindow()
	
		*Usage:* ``if not someApp.hasWindow(): openNewWindow() # some private function`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: True if the app is running and has a main window registered, False otherwise
		
	.. py:method:: getWindow()
	
		*Usage:* ``title = someApp.getWindow()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the title of the frontmost window of this application, might be an empty string
		
	.. py:method:: getPID()
	
		*Usage:* ``pid = someApp.getPID()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the process ID as number if app is running, -1 otherwise
		
	.. py:method:: getName()
	
		*Usage:* ``appName = someApp.getName()`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		:return: the short name of the app as it is shown in the process list

	.. py:method:: setUsing(parametertext)
	
		*Usage:* ``appName = someApp.setUsing("parm1 x parm2 y parm3 z")`` 
		where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
		
		:param parametertext: a string, that is given to the application at startup (when using ``open()`` ) as if you would start the app from a commandline.
	
		:return: the app instance


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

