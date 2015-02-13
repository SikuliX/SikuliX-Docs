How to run SikuliX from Command Line
====================================

SikuliX can be used on command line to run a Sikuli script or open the IDE. 

The usage on each platform:

.. windows::

   :command:`PATH-TO-SIKULIX/runsikulix.cmd [options]`

.. mac::

   :command:`PATH-TO-SIKULIX/runsikulix [options]`

.. linux::

   :command:`PATH-TO-SIKULIX/runsikulix [options]`
   
**runsikulix(.cmd) without any options** simply starts SikuliX IDE.

**PATH-TO-SIKULIX** is the folder containing the Sikuli stuff after having run setup.


Command Line Options (generally, debug output related)
------------------------------------------------------

:program:`PATH-TO-SIKULIX/runsikulix(.cmd)`

.. option::  -d,--debug <value> (up to 3 makes sense)                    

   raise the verbosity level of SikuliX's internal debug messages - might be useful to clarify odd situations
   
.. option::  -f,--logfile [<path to log file>]                    

	 a valid filename (if omitted: WorkingDir/SikuliLog.txt) where SikliX's log messages should be written to
	 
.. option::  -u,--userlog [<path to log file>]                    

   a valid filename (if omitted: WorkingDir/UserLog.txt) where user log messages created using Debug.user() should be written to 


Command Line Options (special)
------------------------------

:program:`PATH-TO-SIKULIX/runsikulix(.cmd)`

.. option::  -h,--help                    

	 print a help message showing the available options and exit
	 
.. option::  -i,--interactive                    

   open an interactive Jython session that is prepared for the usage of the SikuliX features


Command Line Options (intention: IDE should open)
-------------------------------------------------

:program:`PATH-TO-SIKULIX/runsikulix(.cmd)`

.. option::  -c,--console                    

   all output goes to stdout


Command Line Options (intention: run a script without opening the IDE)
----------------------------------------------------------------------

:program:`PATH-TO-SIKULIX/runsikulix(.cmd)`
   
.. option::  -r,--run <sikuli-folder/file>         

   run .sikuli or .skl file
   
   *<sikuli-folder/file>* can be 
    * a relative or absolute path with or without dotted parts (e.g. ../some-script) 
    * a pointer to a location in the HTTP net like so: *<base-url>:script-name* or *<base-url>:folder/script-name* 
      where script-name can be stored and/or accessed without ending .sikuli. The contained script file is downloaded and run,
      while the image files are downloaded when used in the script at runtime.

Command Line Options (intention: provide user parameters for running scripts)
-----------------------------------------------------------------------------

:program:`PATH-TO-SIKULIX/runsikulix(.cmd)`

.. option:: -- <arguments>          

   the space delimeted and optionally quoted arguments are passed to Jython's sys.argv and hence are available to your script
   
