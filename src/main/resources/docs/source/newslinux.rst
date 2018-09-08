.. _newslinux:

Version 1.1.4 - Special for Linux people
========================================

Getting a JDK and SikuliX IDE
-----------------------------

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

Getting the OpenCV support ready
--------------------------------

SikuliX needs access to the OpenCV Java/JNI bindings (an OpenCV native library named ``libopencv_java320.so``, where 320 is a shortcut for the version).

On Debian systems (like Ubuntu) you should get it with the apt-tool, on other Linux systems you might get it with their package-tool. In any case it is possible, to create it from the sources (see below). 

**Generally needed:**

A link (``ln -s ...``) as ``libopencv_java.so`` in any folder, that is on the library path (e.g. on Ubuntu: /usr/lib), to the real library module ``libopencv_javaXYZ.so``

**worked on Ubuntu 18.04**::

        sudo apt install libopencv3.2-java
        ln -s /usr/lib/jni/libopencv_java320.so /usr/lib/libopencv_java.so
        
A ``libopencv_java320.so`` will be in ``/usr/lib/jni``, to where you have to symbolically link ``libopencv_java.so``. On Ubuntu the best place seems to be ``/usr/lib``, but that is up to you, as long as the containing folder is in the current lib path at runtime.


