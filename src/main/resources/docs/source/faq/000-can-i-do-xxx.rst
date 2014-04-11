Can I do X or Y or Z in SikuliX?
================================

If you are wondering if Sikuli can do X or Y, these two general rules apply:

* If you can do X with Java, you can also do it in SikuliX ...

	... by simply adding the respective Java resources to the classpath (the standard Java classes are already there). 
	
	For example, you know how to create a GUI with Java Swing, you can do it in the same way in SikuliX. 

* If you can do X with Python, you probably can do it in SikuliX as well. 

	This actually depends on what Python modules you want to use. SikuliX is on Python language level 2.5 and 2.7. So everything available in the respective Python base package is available in SikuliX too. 
	
	If modules are written in pure Python, you can use them in SikuliX as well. A typical example are the Excel access modules xlrd and xlwt. 
	
	If modules are written in C or depend on C-based stuff, unfortunately, you can't use them in SikuliX (e.g. the support for Win32API calls). 
	
	But it always is a good idea to check in the net, wether there either is a feature compatible module already available for Jython or you might use an appropriate Java library package.

* To Ruby the same rule applies accordingly.

**For any specific question it is always a good idea to first consult** `SikuiX's FAQs and Questions <https://answers.launchpad.net/sikuli>`_

**Then search with Google for the topic mentioning Jython, JRuby or even Sikuli - you will get tons of hints and snippets.** 


Can I write a loop in SikuliX?
------------------------------

Yes. Check the `Python <http://www.jython.org/jythonbook/en/1.0/>`_ or `Ruby <http://docs.ruby-doc.com/docs/ProgrammingRuby>`_ language specs and/or tutorials.  


Can I create a GUI in SikuliX?
------------------------------

Yes. You can create GUIs with Java Swing or any other Java/Jython/JRuby GUI toolkit.


Can I connect to MySQL/MS SQL/PostgreSQL or any database systems in SikuliX?
----------------------------------------------------------------------------

You can use `JDBC <http://www.oracle.com/technetwork/java/javase/jdbc/index.html>`_ or `zxJDBC <http://www.jython.org/jythonbook/en/1.0/DatabasesAndJython.html>`_.


Can I read/write files in SikuliX?
----------------------------------

Yes. Check the `Python <http://www.jython.org/jythonbook/en/1.0/>`_ or `Ruby <http://docs.ruby-doc.com/docs/ProgrammingRuby>`_ language specs and/or tutorials.


