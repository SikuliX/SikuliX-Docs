Interacting with the User and other Applications
================================================

PopUps and input dialogs
------------------------

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
.. py:function:: inputText([msg], [title], [lines], [width])

	:param msg: text to be displayed as message (default: nothing)
	
	:param title: optional title for the messagebox (default: Sikuli Text)
	
	:param lines: how many lines the text box should be high (default: 9)
	
	:param width: how many characters the box should have as width (default: 20)
	
	:return: the possible multiline text entered by the user (might be empty)
	
	A message box with the given height and width is displayed and allows the user to
	input as many lines of text as needed. The display area is fix and not scrollable 
	(means visble is only the top left part, that fits into the visible area).
	But you might enter as much text as you like, e.g. via CopyAndPaste.
	
	Example::
	
	  story = inputText("please give me some lines of text")
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

Listening to Global Hotkeys
---------------------------

Sikuli can listen to global hotkeys that you register with ``Env.addHotkey`` 
and call the corresponding handler (sikuli functions) when the user presses
the hotkeys.

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

Starting and stopping other apllications and bringing their Windows to front
----------------------------------------------------------------------------

Here we talk about the basic features of opening or closing other applications and switching to them (bring
their windows to front).

For the more sophisticated usages including some basic handling of 
application windows look into the :ref:class:`App`.

**General hint for Windows users** on backslashes \\ and double apostrophes "

In a Sikuli script in normal strings enclosed in " (double apostrophes), 
these special characters \\ and " have to be escaped using a backslash, 
when you have them inside the string. So for one backslash you need \\\\ 
and for one " you need \\". In a string enclosed in ' (single apostrophes), a ' 
has to be \\' and a " is taken as such.

To avoid any problems, it is recommended to use the raw string ``r'some text with \\ and " ...'``,
since there is no need for escaping (but no trailing \\ is allowed here). 
  This is especially useful, when you have to specify Windows path's or want to 
  setup command lines for use with App.open(), openApp(), os.popen or Jythons Subprocess module.

a fictive command line example::
	
	cmd = r'c:\Program Files\myapp.exe -x "c:\Some Place\some.txt" >..\log.txt'
	openApp(cmd)

.. py:function:: openApp(application)

	Open the specified application.

	:param application: a string containing the name of an application (case-insensitive), that can be
		found in the path used by the system to locate applications. Or it can be the
		full path to an application.
		
		**Note for Windows:**  The string may contain commandline parameters 
		for the specified program or batch file after the name or full path.

	This function opens the specified application and brings its windows to 
	front. This function may switch to an already opened application or
	may open a new instance of the application depending on system.

	Examples (only to show, might not work on your system)::

		# Windows: opens command prompt (found through PATH)
		openApp("cmd.exe")
		
		#Windows (since X-1.0rc3): with parameters 
		openApp(r'cmd.exe /c start c:\Program Files\myapp.bat')

		# Windows: opens Firefox (full path specified)
		openApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe") 
		
		# Mac: opens Safari
		openApp("Safari")

.. py:function:: switchApp(application)

	Switch to the specified application.

	:param application: the name of an application (case-insensitive) or (part of) a
		window title (Windows/Linux).

	This function switches the focus to the specified application and brings its
	windows to the front. 
	
	*Windows/Linux:* the window is identified by scanning the titles of all 
	accessible windows for the occurence the *application* string. 
	The first window in the system specific order that matches is given focus.

	*Mac:* the string ``application`` is used to identify the application. If the
	application has multiple windows opened, all these windows will be brought to
	the front with unchanged z-order, which cannot be influenced currently. 
	If no application can be found, it is tried to open it.

	Examples::

		# Windows: switches to an existing command prompt or starts a new one
		switchApp("cmd.exe")

		# Windows: opens a new browser window
		switchApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe")

		# Windows: switches to the frontmost opened browser window (or does nothing
		# if no browser window is currently opened)
		switchApp("mozilla firefox")

		# Mac: switches to Safari or starts it
		switchApp("Safari")

.. py:function:: closeApp(application)

	Close the specified application.

	:param application: the name of an application (case-insensitive) or (part of) a
		window title (Windows/Linux)

	This function closes the application indicated by the string *application* (Mac) or
	the windows whose titles contain the string *application* (Windows/Linux).  
	On Windows/Linux, the
	application itself may be closed if the main window is closed or if all the
	windows of the application are closed.

	Example::

		# Windows: closes an existing command prompt
		closeApp("cmd.exe")

		# Windows: does nothing, since text can not be found in the window title
		closeApp("c:\\Program Files\\Mozilla Firefox\\firefox.exe")

		# Windows: stops firefox including all its windows
		closeApp("mozilla firefox")

		# Mac: closes Safari including all its windows
		closeApp("Safari")

.. py:function:: run(command)

	Run *command* in the command line

	:param command: a command that can be run from the command line.

	This function executes the command and the script waits for its completion.
