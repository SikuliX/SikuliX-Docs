.. _newslinux:

Version 1.1.4 - Special for Linux people
========================================

**This is some diary about my trials to get it running on Ubuntu 18.04**

**openJDK 11** using the latest from the `Java 11 EarlyAccess Builds page<http://jdk.java.net/11/>`

 - downloaded the openJDK Linux pack and put the resulting ``jdk11 folder into my home folder``
 - in a Terminal window try ``<home>/jdk11/bin/java -version`` - should show up as Java 11
 
**Downloaded the SikuliX IDE jar** from the `SikuliX Download page<>` and put it in my ``home into a folder SikuliX``

Trying to run it in a Terminal Window being in folder SikuliX as::

    <home>/jdk11/bin/java -jar sikulix.jar -v
    
The parameter ``-v`` gives full debug output from the very beginning - hence keep it until all works.
    
**You might get the following error** message::

    Gtk-Message: 11:28:51.117: Failed to load module "canberra-gtk-module"
    
I found the following solution for that::

    sudo apt install libcanberra-gtk0 libcanberra-gtk-module
    

**now I have to solve the problem**::

    [1802 debug] RunTimeINIT: exists libs folder at: /home/raiman/.Sikulix/SikulixLibs
    [1804 debug] RunTimeINIT: libs folder empty or has wrong content
    [1817 debug] RunTimeINIT: export libs from: null
    Exception in thread "main" java.lang.NullPointerException
      at org.sikuli.script.RunTime.libsExport(RunTime.java:894)
      at org.sikuli.script.RunTime.init(RunTime.java:695)
      at org.sikuli.script.RunTime.get(RunTime.java:375)
      at org.sikuli.ide.SikulixRunIDE.main(SikulixRunIDE.java:32)
      
**See you later - aligator**
