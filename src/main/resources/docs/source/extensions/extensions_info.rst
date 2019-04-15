General Information About Sikuli Extensions
===========================================

.. _sikuliextensions:

**This feature is currently under developement**

Extensions allow to implement additional features to the SikuliX environment by adding packages to your current SikuliX installation.

Extensions are finally stored in the ``AppData area in the folder Extensions`` either manually or with support by SikuliX at start-up.

Additionally a file ``extensions.txt`` in this folder will be evaluated at startup. It may contain absolute filenames of extensions stored elsewhere on the system. It may also contain key-value-pairs for special purposes.

Currently defined and accepted:
 - ``jython = <path-to>/jython.jar`` pointer to outside SikuliX installed Jython. If there, it takes precedence over the standard SikuliX Jython package.
 - ``python = <path-to>/<python-executable>`` (experimental) allows to run scripts with the real Python interpreter (:ref:`for details see<RealPython>`)

Jar-files will be added to the Java classpath. For other filetypes there are special treatments to be implemented.

How to Download and use an Extension
------------------------------------

The download of SikuliX extensions will be supported by the IDE through the ``menu Tools -> Extensions`` (under development).

But as mentioned above, you can use your own extensions.

**How to Use an Extension**

To use the features of an available extension in one of your scripts, it depends on waht API the extension offers:

*Java API*
    import and access the Java stuff as defined for using Java in Jython scripts

*Jython API*
    if the extension contains one or more Python modules, just use ``load("name-of-extension.jar")`` to make it available for later imports.

For information about features, usage and API use menu :menuselection:`Tools -> Extensions -> More Info` in the IDE.

How to develop an extension
---------------------------

The **source structure** of an extension named ``extension-name`` looks like this: ::

	Java
	- org/com
	-- your-organization-or-company
	--- extension-name
	---- yourClass1.java
	---- yourClass2.java
	---- .... more classes
	python
	- extension-name
	-- __init__.py
	-- extension-name.py
	
The **final structure of a JAR** (filename ``extension-name-X.Y`` where X.Y is the version string) looks like this: ::
	
	org/com
	- your-organization-or-company
	-- extension-name
	--- yourClass1.class
	--- yourClass2.class
	--- .... more classes
	extension-name
	- __init__.py
	- extension-name.py
	META-INF
	- MANIFEST.MF

The file ``__init__.py`` contains at least ``from extension-name import *`` to avoid one qualification level. So in a script you might either use::

    load("extension-name.jar")
    import extension-name
    extension-name.functionXYZ()
	
or::

    load("extension-name.jar")
    from extension-name import *
    functionXYZ()
	
The second case requires more investement in a naming convention, that avoids naming conflicts.

The file ``extension-name.py`` contains the classes and methods, that represent the API, that one might use in a Sikuli script. 

Name your extensions properly
-----------------------------

Sikuli extensions can be Python/Jython modules and/or Java classes.

For Java classes, following the reverse URL convention of Java is a good idea (for example, org.foo.your-extension). However, **DO NOT use Java's convention for Python/Jython modules**. You need to come up with a unique extension name that does not conflict with existing Python modules and other Sikuli extensions.

Please read `Naming Python Modules and Packages <http://jythonpodcast.hostjava.net/jythonbook/en/1.0/ModulesPackages.html#naming-python-modules-and-packages>`_ to learn the details for naming a Python module.
