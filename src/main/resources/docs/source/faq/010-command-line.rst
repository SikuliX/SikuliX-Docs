.. index:: run scripts from command line

.. _RunningScriptsFromCommandLine:

How to run SikuliX from Command Line
====================================

SikuliX can be used on command line to run a Sikuli script or open the IDE. 

**PATH-TO-SIKULIX** is the path to the SikuliX jar files (IDE: sikulix.jar and/or API:sikulixapi.jar)

:program:`java -jar PATH-TO-SIKULIX option option ...`

**Note** up to version 1.1.3 there are command files in the bundle that might be used alternatively.

Command Line Options (generally, debug output related)
------------------------------------------------------

.. option::  -v                    

   debug messages from the very beginning, might be useful to clarify odd situations during startup

.. option::  -q                    

   completely switch off any SikuliX created output (see the log file options)
   
.. option::  -d,--debug <value> (up to 3 makes sense)                    

   raise the verbosity level of SikuliX's internal debug messages - might be useful to clarify odd situations
   
.. option::  -f,--logfile [<path to log file>]                    

	 a valid filename (if omitted: WorkingDir/SikuliLog.txt) where SikliX's log messages should be written to
	 
.. option::  -u,--userlog [<path to log file>]                    

   a valid filename (if omitted: WorkingDir/UserLog.txt) where user log messages created using Debug.user() should be written to 


Command Line Options (special)
------------------------------

.. option::  -h,--help                    

	 print a help message showing the available options and exit
	 
	 
Command Line Options (intention: IDE should open)
-------------------------------------------------

.. option::  -c,--console                    

   all output goes to stdout


Command Line Options (intention: run a script without opening the IDE)
----------------------------------------------------------------------
   
.. option::  -r,--run <folder/file>  (one or more entries seperated by space)       

   run one script or more scripts one after the other skipping entries, that do not represent valid scripts.
   
   *<sikuli-folder/file>* can be 
    * a relative or absolute path/filename with or without dotted parts (e.g. ../some-script) 
    * a pointer to a location in the HTTP net (:ref:`for details look here <RunningScripts>`). The contained script file is downloaded and run,
      while the image files are downloaded when used in the script at runtime.
   
   Having more than one script, the folder containing the first script is remembered and applied 
   to the following entries being a relative path/filename - example:
      ``- r MyScripts/test1 test2 test2`` will reuse the location ``<working-folder>/MyScripts/`` for test2 and test3
      
   A folder (first entry or intermediate) ``you-name-it`` not being a valid script folder (not ending ``.sikuli``, not containing a ``you-name-it.py``)
   will be taken as such and get the base folder for the following relative paths/filenames.
      
   Having more than one script specified: a return code of -1 will stop the complete execution.
   
   Having more than one script specified: the next script can get the return code of the script run before using
   ``ScriptingSupport.getLastReturnCode()``
    

Command Line Options (intention: run the experimental scriptrun server)
-----------------------------------------------------------------------
   
.. option::  -s,--server [<port>]  (optional port not yet supported, 50001 is used as default)       

   start a scriptrun server (`more information<http://www.sikulix.com/support.html>`_)


Command Line Options (intention: provide user parameters for running scripts)
-----------------------------------------------------------------------------

.. option:: -- <arguments>          

   the space delimited and optionally quoted arguments (only apostrophes are supported) are passed to Jython's sys.argv and hence are available in your script. A parameter containing intermediate blanks MUST be quoted to get it into one sys.argv entry.
   
   This option must go after all the other options mentioned above.
