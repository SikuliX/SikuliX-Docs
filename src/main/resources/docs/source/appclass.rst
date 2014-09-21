The Application Class (App)
===========================

.. py:class:: App

Using class or instance methods
-------------------------------

Generally you have the choice between using the class methods (e.g.
``App.open("application-identifier")``) or first create an App instance and use
the instance methods afterwards (e.g. ``myApp = App("application-identifier")``
and then later on ``myApp.open()``). In the current state of the feature
developement of the class App, there is no recomendation for a preferred usage.
The only real difference is, that you might save some ressources, when using the
instance approach, since using the class methods produces more intermediate
objects. 

.. _CreateAppInstance:

**How to create an App instance**

The basic choice is to just say ``someApp = App("some-app-identifier")`` and you
have your app instance, that you can later on use together with its methods,
without having to specify the string again. 

Additionally ``App.open("some-app-identifier")`` and ``App.focus("some-app-identifier")``
return an app instance, that you might save in a variable to use it later on in
your script. 

Differences between Windows, Linux and Mac
------------------------------------------

Windows/Linux: Sikuli's strategy on these systems in the moment is to rely on
implicit or explicit path specifications to find an application, that has to be
started. Running "applications" can either be identified using their PID
(process ID) or by using the window titles. So using a path specification will
only switch to an open application, if the application internally handles the
"more than one instance" situation".

You usually will use ``App.open("c:\\Program Files\\Mozilla Firefox\\Firefox.exe")``
to start Firefox. This might open an additional window. And you can use
``App.focus("Firefox")`` to switch to the frontmost Firefox window (which has no
effect if no window is found). To clarify your situation you may use the new
window() method, which allows to look for existing windows. The second possible
approach is to store the App instance, that is returned by ``App.open()``, in a
variable and use it later on with the instance methods (see examples below).

If you specify the exact window title of an open window, you will get exactly
this one. But if you specify some text, that is found in more than one open
window title, you will get all these windows in return. So this is good e.g.
with Firefox, where every window title contains "Mozilla Firefox", but it might
be inconvenient when looking for "Untitled" which may be in use by different
apps for new documents. So if you want exactly one specific window, you either
need to know the exact window title or at least some part of the title text,
that makes this window unique in the current context (e.g. save a document with
a specific name, before accessing it's window).

On Mac OS X, on the system level the information is available, which windows
belong to which applications. Sikuli uses this information. So by default using
e.g. ``App.focus("Safari")`` starts Safari if not open already and switches to the
application Safari if it is open, without doing anything with it's windows (the
z-order is not touched). Additionally, you can get all windows of an
application, without knowing it's titles.

Note on Windows: when specifying a path in a string, you have to use \\ (double
backslash) for each \ (backslash)
e.g. ``myPath = "c:\\Program Files\\Sikuli-IDE\\Lib\\"`` )

Open, close and focus an application
------------------------------------

.. _ClassAppMethods:

.. py:class:: App
  
	.. py:classmethod:: open(application)
	
		*Usage:* ``App.open(application)``

		Open the specified application.

		:param application: The name of an application (case-insensitive), that can
			be found in the path used by the system to locate applications, or the
			full path to an application (Windows: use double backslash \\ in the
			path string to represent a backslash)
			
			**Note for Windows:** (since X-1.0rc3) The string may contain commandline parameters 
			for the specified program or batch file after the name or full path (see: :py:func:`openApp`)

			
		:return: an App object, that can be used with the instance methods.
		
		This method is functionally equivalent to :py:func:`openApp`. It opens the
		specified application and brings its window the front. Whether this
		operation switches to an already opened application or opens a new instance
		of the application depends on the system and application.

	.. py:method:: open()
	
		*Usage:* ``someApp.open()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.
	
		Open this application.


	.. py:classmethod:: focus(application)

		*Usage:* ``App.focus(application)``

		Switch the focus to an application.

		:param application: The name of an application (case-insensitive) or (part
			of) a window title (Windows/Linux).

		:return: an App object, that can be used with the instance methods.
		
	.. py:method:: focus()
	
		*Usage:* ``someApp.focus()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Switch the focus to this application.


	.. py:classmethod:: close(application)
	
		*Usage:* ``App.close(application)``

		Close the specified application.

		:param application: The name of an application (case-insensitive) or (part
			of) a window title (Windows/Linux).

		This method is functionally equivalent to :py:func:`closeApp`. It closes the
		given application or the matching windows (Windows/Linux). It does nothing
		if no opened window (Windows/Linux) or running application (Mac) can be
		found. On Windows/Linux, whether the application itself is closed depends on
		weather all open windows are closed or a main window of the application is
		closed, that in turn closes all other opened windows. 

	.. py:method:: close()

		*Usage:* ``someApp.close()`` where App instance ``someApp`` was :ref:`created before <CreateAppInstance>`.

		Close this application.

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
			with firefox.window(): # see the general notes below
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

