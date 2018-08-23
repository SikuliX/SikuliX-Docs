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
    

**The IDE starts up and principally works**

**now I have to solve the problems:**

 - create the libs for openCV and tesseract and make them available.

**See you later - aligator**
