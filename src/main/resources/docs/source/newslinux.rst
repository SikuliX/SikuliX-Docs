.. _newslinux:

Version 2.0.2+ - Special for Linux people
========================================

Getting a JDK and the SikuliX packages (IDE and/or API)
-------------------------------------------------------

You need a Java JDK version 8 or later.
 
**Download the SikuliX IDE jar** from the `SikuliX Download page <https://raiman.github.io/SikuliX1/downloads.html>`_
and put it in your ``home into a folder SikuliX``

Try to run it in a Terminal Window being in folder SikuliX as::

    <home>/jdk11/bin/java -jar sikulix.jar -v
    
The parameter ``-v`` gives full debug output from the very beginning - hence keep it until everything works.
    
**You might get the following error**
    Gtk-Message: 11:28:51.117: Failed to load module "canberra-gtk-module"
    solution: ``sudo apt install libcanberra-gtk0 libcanberra-gtk-module``

**You might get the following error**
    Assistive technology not found AWTError
    `look here for possible solutions <https://askubuntu.com/questions/695560/assistive-technology-not-found-awterror>`_

**The IDE should start up and the GUI should principally work.**

**Be aware** To make it finally useable, you have to **make sure, that your system supports OpenCV and Tesseract.**.

... and if you want to use the **App class features** you still need ``xdotool`` and ``wmctrl``.

**For programming with Java** you might use the ready to use ``sikulixapi.jar`` or the Maven snapshot dependency
in a Maven/Gradle project. In any case you have to make ``OpenCV`` and ``Tesseract`` ready before.

Getting the OpenCV support ready
--------------------------------

.. note:

        SikuliX 2.0.x: needs OpenCV 3.x preferably 3.4.x   ---   SikuliX 2.1.0+: needs OpenCV 4.x

SikuliX needs access to the OpenCV Java/JNI bindings (an OpenCV native library named ``libopencv_javaXYZ.so``,
where XYZ is a shortcut for the version like 320 or 341).

On Debian systems (like Ubuntu) you should get it with the apt-tool, on other Linux systems you might
get it with their package-tool. In any case it is possible, to create it from the sources (see below).

**Generally needed:**

A link (``ln -s ...``) as ``libopencv_java.so`` in any folder, that is on the library path
(e.g. on Ubuntu: /usr/lib), to the real library module ``libopencv_javaXYZ.so``

**should work on Ubuntu 18.04 (or other actual Debian systems)**::

        sudo apt install libopencv3.2-java
        sudo ln -s /usr/lib/jni/libopencv_java320.so /usr/lib/libopencv_java.so
        
A ``libopencv_java320.so`` will be in ``/usr/lib/jni``, to where you have to symbolical link ``libopencv_java.so``.
On Ubuntu the best place seems to be ``/usr/lib``, but that is up to you,
as long as the containing folder is in the current lib path at runtime.

**Build OpenCV library from the sources (might be needed on non-Debian Linux)**

Get a `source package (recommended: 3.4+) <https://opencv.org/releases.html>`_
and unzip/untar it to a work folder of your choice.

In a terminal session go into the OpenCV folder and make a folder ``build`` at the top level.
Finally go into that ``build`` folder

All the following steps have to be done being inside this ``build`` folder.

There are some general prerequisites for the build process to work, that you have to fulfill either before you start
or solve it every time you run into a problem. On Ubuntu, the usual way is to use the apt-tool.

You need: cmake, ant, build-tools, Python (2.7 or 3.x) and a Java JDK

All prerequisites must be available/accessible in the standard environment.

In the build folder run the build setup::

    cmake ..

As a result you will get some output, which tells you,
wether you will have a chance to get a valid ``libopencv_javaXYZ.so``

It should look something like that towards the end::

    -- General configuration for OpenCV 3.4.2 =====================================

    ... more

    --   C/C++:
    --     Built as dynamic libs?:      YES

    ... more

    --   OpenCV modules:
    --     To be built:                 core features2d flann imgcodecs imgproc highgui |... maybe more ...| 
                                        java java_bindings_generator python_bindings_generator
    --     Disabled:                    
    --     Disabled by dependency:      |... see comment below ...|
    --     Unavailable:                 cudaarithm cudabgsegm cudacodec cudafeatures2d cudafilters cudaimgproc cudalegacy cudaobjdetect cudaoptflow cudastereo cudawarping cudev dnn python2 python3 ts viz

    ... more

    --   Media I/O:
    --     ZLib:                        /usr/lib/x86_64-linux-gnu/libz.so (ver 1.2.11)
    --     JPEG:                        build-libjpeg-turbo (ver 1.5.3-62)
    --     WEBP:                        build (ver encoder: 0x020e)
    --     PNG:                         build (ver 1.6.34)
    --     TIFF:                        build (ver 42 - 4.0.9)
    --     JPEG 2000:                   build (ver 1.900.1)
    --     OpenEXR:                     build (ver 1.7.1)
    --     HDR:                         YES
    --     SUNRASTER:                   YES
    --     PXM:                         YES
    --
    --   Video I/O:

    .. more

    --   Python (for build):            /usr/bin/python3
    --
    --   Java:
    --     ant:                         /usr/bin/ant (ver 1.10.3)
    --     JNI:                         /usr/lib/jvm/java-10-oracle/include /usr/lib/jvm/java-10-oracle/include/linux /usr/lib/jvm/java-10-oracle/include
    --     Java wrappers:               YES
    --     Java tests:                  NO
    --
    --   Matlab:                        NO
    --
    --   Install to:                    /usr/local
    -- -----------------------------------------------------------------
    --
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/raiman/SikuliX/opencv-3.4.2/build

**Some notes (top down):**

 - ``Built as dynamic libs``: you will have to install the built stuff to your system in a second step.
 - ``OpenCV modules``: 
   - the list in the line after ``To be built`` MUST contain ``java``.
   - at least core features2d flann imgcodecs imgproc highgui must be there
 - ``Media I/O``: at least ZLib, JPEG and PNG must show a valid entry
 - ``Python (for build)``: must show a valid Python runner
 - ``Java``: ``ant`` and ``JNI`` must show valid entries
 - ``Install to``: must make sense

If one or more of the mentioned entries are odd, you have to debug for the reason ;-)

Common problems are (if ``java`` is not mentioned in the line ``To be built``),
that a Python is not available and a valid JDK is not found in the environment.

The usual way to solve the problems is to install the relevant packages.

Each time you fixed something simply run ``cmake ..`` again.

If you made any changes to ``CMakeCache.txt`` just run ``cmake ..`` again.

When you are finally satisfied with the result, just run::

    make install

This will install the stuff into your system (will take some minutes to complete).
Be sure, that you have a success: no errors are shown.

As a last step you need to find the installed ``libopencv_javaXYZ.so`` and as mentioned above create a symbolic link,
that is found in the library path.

Getting the Tesseract support ready
-----------------------------------

You have to make sure, that a ``libtesseract....so`` is available on your system.

You should first try with your package manager.

**It must be a version 4.x** 

To test, what you have on your system, run this::

        tesseract -v

        # should show up with something like that:
        tesseract 4.1.0
         leptonica-1.78.0
          libgif 5.1.4 : libjpeg 9c : libpng 1.6.37 : libtiff 4.0.10 : zlib 1.2.11 : libwebp 1.0.3 : libopenjp2 2.3.1
         Found AVX2
         Found AVX
         Found SSE

If anything looks odd, then you have to dive into the gory details.
