Interacting with the User and other Applications
================================================

PopUps and input dialogs
------------------------

In the standard the following dialog boxes are shown in the middle of the screen, where SikuliX (IDE or from commandline) is running (usually the primary screen).

.. versionadded:: 1.1.1
They are always on top from the beginning, no matter which application currently is the frontmost. While the dialog is visible, you might move it around and act on other applications, until you work with the dialog box.

.. versionadded:: 1.1.1
If you want the dialog to appear in a special location on the screen (even on other screens in multimonitor situations), you can use the function :py:func:`popat` to define this location. The dialog will be positioned here with the center of its dialog panel. Be aware, that locations near the edge of the screen might make parts of the dialog not accessible (this is not checked). This location will stay in effect until changed by another use of :py:func:`popat`. A :py:func:`popat` without parameters will reset it to the standard (center of primary screen).

**Note for Java Usage** These methods are available in class ``org.sikuli.script.Sikuli``

.. py:function:: popat(x, y)
.. py:function:: popat(location)
.. py:function:: popat(region)
.. py:function:: popat()

	Define the location, where the center of popup dialogs should be positioned from now on.
	
	:param x: x value of the location
	:param y: y value of the location
	
	:param location: the location as a :py:class:`Location`
	
	:param region: the location as the center of the given :py:class:`Region`

	If no parameter is given, the location will be reset to the center primary screen (default).

.. py:function:: popup(text, [title])

	Display a dialog box with an *OK* button and *text* as the message. The script
	then waits for the user to click the *OK* button.
	
	:param text: text to be displayed as message
	
	:param title: optional title for the messagebox (default: Sikuli Info)

	Example::

		popup("Hello World!\nHave fun with Sikuli!")
	
	A dialog box that looks like below will popup
	  *Note:* `\\n` inserts a line break

	.. image:: popup.png

.. versionadded:: 1.1.0
.. py:function:: popError(text, [title])

	Same as :py:func:`popup` but with a different title (default Sikuli Error) and alert icon.

	Example::

		popError("Uuups, this did not work")
	
	A dialog box that looks like below will popup

	.. image:: popError.png

.. versionadded:: 1.1.0
.. py:function:: popAsk(text, [title])

	:return: ``True`` if user clicked ``Yes``, ``False`` otherwise

	Same as :py:func:`popup` but with a different title (default Sikuli Decision) and alert icon.
	
	There are 2 buttons: ``Yes`` and ``No`` and hence the message text should be written as an
	appropriate question.

	Example::

		answer = popAsk("Should we really continue?")
		if not answer: 
		    exit(1)
	
	A dialog box that looks like below will popup

	.. image:: popAsk.png

.. versionadded:: 1.1.0
.. py:function:: input([msg], [default], [title], [hidden])

	Display a dialog box with an input field, a Cancel button, and an OK button. The
	optional text *title* is displayed as the messagebox title and the text *msg* as some explanation 
	near the input field. The script then waits for the
	user to click either the Cancel or the OK button.
	
	:param msg: text to be displayed as message (default: nothing)
	
	:param default: optional preset text for the input field
	
	:param title: optional title for the messagebox (default: Sikuli Input)
	
	:param hidden: (default: False) if true the entered characters are shown as asterisks 
	
	:return: the text, contained in the input field, when the user clicked ``Ok``

		**None**, if the user pressed the ``Cancel`` button or closed the dialog

	Example: plain input::

		name = input("Please enter your name to log in:")
		
	.. image:: input.png
	
	A dialog box that looks like above will appear to allow the user to
	interactively enter some text. This text is then assigned to the variable
	*name*, which can be used in other parts of the script, such as ``paste(name)``
	to paste the text to a login box.
	
	Example: input with preset::

		name = input("Please enter your name to log in:", "anonymous") # a preset input text
		
	.. image:: inputPreset.png

	When using the parameter ``default``, the text input field will be pre-populated with the 
	given text, so the user might just click OK/Cancel or edit the content of the input field.
	
	Example: input with hidden input::

		password = input("please enter your secret", hidden = True)
		
	.. image:: inputHidden.png

	As the user inputs his secret infoemation, the text is shown as one asterisk per character.
	
.. versionadded:: 1.1.0
.. py:function:: inputText(message, [title=""], [lines=9], [width=20], [text=""])

	:param message: text to be displayed as message 
	
	:param title: optional title for the messagebox (default: SikuliX input request)
	
	:param lines: how many lines the text box should be high (default: 9)
	
	:param width: how many characters the box should have as width (default: 20)
	
	:param text: a multiline text, that is preset in the textarea
	
	:return: the multiline text content when user presses ``OK`` (might be empty) or None if the user presses ``CANCEL``
	
	A message box with the given height and width is displayed and allows the user to
	input as many lines of text as needed. The lines are auto-wrapped at word boundary. 
	A vertical scrollbar is shown if needed.
	
	The default font is the Java AWT Dialog (a sans-serif font) in size 14, which is also the minimum size possible. One might switch to a monospace font using ``Settings.InputFontMono=True``. Setting it to ``False`` switches it back to the standard for the next ``inputText()``. 
	
	A bigger size than 14 can be set using ``Settings.InputFontSize=NN``. Setting it to a value smaller than 14 (e.g. 0) will reset it to 14 again. 
	
	Example::
	
	  # selects a monospaced font
	  # default is False meaning a SansSerif font
	  Settings.InputFontMono = True

	  # default fontsize is 14 (also minimum size)
	  # use a fontsize of 20
	  Settings.InputFontSize = 20

	  story = inputText("please tell a story")
	  lines = story.split("\n") # split the lines in the list lines
	  for line in lines:
	     print line

	.. image:: inputText.png

.. versionadded:: 1.1.0
.. py:function:: select([msg], [title], [options], [default])

	:param msg: text to be displayed as message (default: nothing)
	
	:param title: optional title for the messagebox (default: Sikuli Selection)
	
	:param options: a list of text items (default: empty list, nothing done)
	
	:param default: the preselected list item (default: first item)
	
	:return: the selected item (might be the default)
	
	Displays a dropdown menu containing the given options list items with the default selected.
	The user might select one item and click ok.
	
	Example::
	  
	  items = ("nothing selected", "item1", "item2", "item3")
	  selected = select("Please select an item from the list", options = items)
	  if selected == items[0]:
	     popup("You did not select an item")
	     exit(1)
	
	.. image:: select1.png

	.. image:: select2.png
	
.. versionadded:: 1.1.1
.. py:function:: popFile([title])

	Display a file open dialog, that lets the user select a folder or file.
	
	:param title: optional title for the dialogbox (default: Select a file or folder)
	
	:return: the absolute path of the selected file or folder as a string

	
Listening to Global Hotkeys
---------------------------

Sikuli can listen to global hotkeys that you register with ``Env.addHotkey`` 
and call the corresponding handler (sikuli functions) when the user presses
the hotkeys.

**BE AWARE** Be sure, that the key combination you use is free and not used by the system or any other application. The hotkey feature may not report an error in such situations and as a consequence your hotkey definition simply does not work as expected. An example is the F12 key on Windows alone or with SHIFT, which in the standard is occupied by the system as global debugging key (might be released by hacking the registry).

.. py:method:: Env.addHotkey(key, modifiers, handler)

 	Register the specified *key* + *modifiers* as a global hotkey. 
 	When the hotkey is pressed, the specified function *handler* will be called.

	:param key: a character or a constant value defined in :py:class:`Key`.

	:param modifiers: Key modifiers, which can be one or multiple constants defined in :py:class:`KeyModifier`.

	:return: True if success.

        .. sikulicode::

           def openAppleMenu(event):
              click("apple.png")

           # When the user pressed Ctrl+Alt+F1, click the top-left apple icon.
           Env.addHotkey(Key.F1, KeyModifier.ALT+KeyModifier.CTRL, openAppleMenu)


.. py:method:: Env.removeHotkey(key, modifiers)

 	Unregister the registered global hotkey *key* + *modifiers*. 

	:param key: a character or a constant value defined in :py:class:`Key`.

	:param modifiers: Key modifiers, which can be one or multiple constants defined in :py:class:`KeyModifier`.

	:return: True if success.
	
**A more generic example**

It keeps the handlers free from processing code, just signals the keypress using a global variable to the main loop. The main loop simply permanently scans the global variables and then does what has to be done.

The whole process is blocking in the sense, that hotkeys are processed one after the other in the sequence they appear in the main loop and each hotkey is only recognized again, after its current press is processed in the main loop.

This setup keeps things more transparent and straightforward. Other setups even with threading are possible, but need much more effort to correctly synchronize the processing especially when mouse or keyboard actions are involved.

  .. pycode::

    # hotkey to stop the script
    hotKeyX = False; # global to communicate with main loop
    def xHandler(event):
      global hotKeyX
      hotKeyX = True # tell main loop that hotkey was pressed
    # add the hotkey with its handler
    Env.addHotkey("x", KeyModifier.CTRL + KeyModifier.SHIFT, xHandler)

    # function hotkey: something to do when pressed
    hotKeyN = False;
    def nHandler(event):
      global hotKeyN
      hotKeyN = True
    Env.addHotkey("n", KeyModifier.CTRL + KeyModifier.SHIFT, nHandler)

    # the main loop, that simply waits for pressed hotkeys
    # which are then processed
    count = 0;
    while True:
      if (hotKeyX):
        popup("processing ctrl+shift+x: stopping")
        exit()
      if (hotKeyN):
        hotKeyN = False # reset the hotkey variable
        # and now do something
        count += 1
        popup("processing ctrl+shift+n: %d" % count)
      wait(1)

Starting and stopping other applications and bring them to front
----------------------------------------------------------------

.. versionadded:: 1.1.0
Completely revised in version 1.1.0

Here we talk about the basic features of opening or closing other applications and switching to them (bring
them to front).

For the more sophisticated usages including some basic handling of 
application windows look class :py:class:`App`.

You can use the feature run(someCommand) to delegate something, you can do on a commandline, to a seperate process.
The script waits for completion and you have acces to the return code and 
the output the command has produced.

**NOTE on Java usage** At the Java level only the features of the App class are available (class :py:class:`App`).

**General hint for Windows users** on backslashes \\ and double apostrophes "

In a Sikuli script in normal strings enclosed in " (double apostrophes), 
these special characters \\ and " have to be escaped using a backslash, 
when you have them inside the string. So for one backslash you need \\\\ 
and for one " you need \\". In a string enclosed in ' (single apostrophes), a ' 
has to be \\' and a " is taken as such.

To avoid any problems, it is recommended to use the raw string ``r'some text with \ and " ...'``,
since there is no need for escaping (but no trailing \\ is allowed here). 
  This is especially useful, when you have to specify Windows path's containing blanks or want to 
  setup command lines for use with openApp(), App.open(), run(), os.popen() or Jythons Subprocess module.
  
**NOTE for Mac users** As application name use the name, that is displayed with the program symbol on the taskbar, 
which might differ from what is displayed in the top left of the menu bar.

Example: The Chrome browser displays "Chrome" in the menu bar, but the application name is "Google Chrome".
So openApp("chrome") will fail, whereas openApp("google chrome") will do the job. 
Same goes for switchApp() and closeApp().

.. py:function:: openApp(application)

	Open the specified application, or swith to it, if it is already open.
	
	:param application: a string containing the name of an application (case-insensitive), that can be
		found in the path used by the system to locate applications. Or it can be the
		full path to an application.
	
	:return None if an error occured, on success a new App class object (look :py:class:`App`)
		
	This function opens the specified application and brings it to front. 
	It might switch to an already opened application, if this can be identified in the process list.
	
	**Windows:** A running instance will be ignored in any case
	and hence in most cases a new instance of the program will be started.
	
	Examples::
	
		# Windows: run a batch file in a new command window:
		`òpenApp("cmd.exe /c start path-to-some.bat")``

		# Windows: opens Firefox (full path specified)
		``openApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe")`` or
		``openApp(r"c:\Program Files\Mozilla Firefox\firefox.exe")``
		
		# Mac: opens Safari
		``openApp("Safari")``

.. py:function:: switchApp(application)

	Bring the matching application or window to front (make it the active/focused application/window).
	If no matching application/window can be found, 
	it is tried to open an application using the given string as program name or location.

	:param application: the name of an application (case-insensitive) or (part of) a
		window title (Windows/Linux) (case-sensitive).

	:return None if an error occured, on success a new App class object (look :py:class:`App`)
		
	This function switches the input focus to the specified application (brings it to front).
	
	*Windows:* In the first step, the given text is taken as part of a program name (not case sensitive). 
	If it is found in the process list, it will be switched to front, if it has a main window 
	(registered in the process list). Otherwise the text will be used to search for a matching window title.
	
	*Windows/Linux:* the window is identified by scanning the titles of all 
	accessible windows for the occurence of the *application* string. 
	The first window in the system specific order, whose title contains the given text, is given focus.

	*Mac:* the string ``application`` is used to identify the application. If the
	application has multiple windows opened, all these windows will be brought to
	the front with unchanged z-order, which cannot be influenced currently. 
	

	Examples::

		# Windows: switches to an already opened Firfox or opens it otherwise
		switchApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe")

		# Windows: switches to the frontmost opened browser window (or does nothing
		# if no Firefox window is currently opened)
		# works, because all Firefox window titles contain "Mozilla Firefox"
		switchApp("Mozilla Firefox")

		# Mac: switches to Safari or starts it
		switchApp("Safari")

.. py:function:: closeApp(application)

	Close the specified application.

	:param application: the name of an application (case-insensitive) or (part of) a
		window title (Windows/Linux)

	:return None if an error occured, on success a new App class object (look :py:class:`App`)

	This function closes the application indicated by the string *application* (Mac) or
	the windows whose titles contain the string *application* (Windows/Linux).  
	On Windows/Linux, the application itself may be closed if the main window is closed or if all the
	windows of the application are closed.

	Example::

		# Windows: closes Firefox if it is running, does nothing otherwise
		closeApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe")

		# Windows: stops firefox including all its windows
		closeApp("Mozilla Firefox")

		# Mac: closes Safari including all its windows
		closeApp("Safari")

.. py:function:: run(command)

	Run *command* in the command line

	:param command: a command that can be run from the command line.
	
	:return: a multiline string containing the result of the execution

	This function executes the command and the script waits for its completion.
	
	**structure of the result** (comments after #, not part of the result)
	
	Multiline string::
		
		N # a number being the return code
		text
		text
		text
		text # no, one or more lines execution output (stdout)
		*****error***** # if the execution ended with an error
		error text # or the return code was not 0
		error text
		error text # no, one or more lines error output (stderr)
		
**NOTE** for usage variants of the command run() and for the Java usage see class :py:class:`App`
