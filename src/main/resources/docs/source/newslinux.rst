.. _newslinux:

Version 1.1.4 - Special for Linux people
========================================

Getting a JDK and SikuliX IDE
-----------------------------

**This is some diary about my trials to get it running on Ubuntu 18.04**

**openJDK 11** using the latest from the `Java 11 EarlyAccess Builds page <http://jdk.java.net/11/>`_

 - downloaded the openJDK Linux pack and put the resulting ``jdk11 folder into my home folder``
 - in a Terminal window try ``<home>/jdk11/bin/java -version`` - should show up as Java 11
 
**Downloaded the SikuliX IDE jar** from the `SikuliX Download page <https://raiman.github.io/SikuliX1/downloads.html>`_
and put it in my ``home into a folder SikuliX``

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

A link (``ln -s ...``) as ``libopencv_java.so`` in any folder, that is on the library path
(e.g. on Ubuntu: /usr/lib), to the real library module ``libopencv_javaXYZ.so``

**worked on Ubuntu 18.04**::

        sudo apt install libopencv3.2-java
        ln -s /usr/lib/jni/libopencv_java320.so /usr/lib/libopencv_java.so
        
A ``libopencv_java320.so`` will be in ``/usr/lib/jni``, to where you have to symbolically link ``libopencv_java.so``.
On Ubuntu the best place seems to be ``/usr/lib``, but that is up to you,
as long as the containing folder is in the current lib path at runtime.

**build OpenCV library from the sources**

Get a `source package (recommended: 3.4+) <https://opencv.org/releases.html>`_
and unzip/untar it to a work folder of your choice.

In a terminal session go into the source folder and make a folder ``build``.

All the following steps have to be done in this folder.

There are some general prerequisites for the build process to work, that you have to fulfill either before you start
or every time you run into a problem. On Ubuntu, the usual way is to use the apt-tool.

You need: cmake, ant, build-tools, Python (2.7 or 3.x), a Java JDK

All prerequisites must be available in the standard environment.

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
    --     To be built:                 core features2d flann imgcodecs imgproc java java_bindings_generator python_bindings_generator
    --     Disabled:                    calib3d highgui js ml objdetect photo shape stitching superres video videoio videostab world
    --     Disabled by dependency:      -
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

Some notes (top down):

 - ``Built as dynamic libs``: you will have to install the built stuff to your system in a second step.
 - ``OpenCV modules``: the list in the line after ``To be built`` MUST contain ``java``.
 - ``Media I/O``: at least ZLib, JPEG and PNG must show a valid entry
 - ``Python (for build)``: must show a valid Python runner
 - ``Java``: ``ant`` and ``JNI`` must show valid entries
 - ``Install to``: must make sense

If one or more of the mentioned entries are odd, you have to debug for the reason ;-)

Common problems are (if ``java`` is not mentioned in the line ``To be built``),
that a Python is not available and a valid JDK is not found in the environment.

The usual way to solve the problems is to install the relevant packages.

You may fine tune, what you get by editing the file ``CMAKE_CACHE.txt`` and run ``cmake ..`` again.

The relevant section near the top of ``CMAKE_CACHE.txt``::

    //Build CUDA modules stubs when no CUDA SDK
    BUILD_CUDA_STUBS:BOOL=OFF

    //Create build rules for OpenCV Documentation
    BUILD_DOCS:BOOL=OFF

    //Build all examples
    BUILD_EXAMPLES:BOOL=OFF

    //Create Java wrapper exporting all functions of OpenCV library
    // (requires static build of OpenCV modules)
    BUILD_FAT_JAVA_LIB:BOOL=OFF

    //Build IPP IW from source
    BUILD_IPP_IW:BOOL=ON

    //Build Intel ITT from source
    BUILD_ITT:BOOL=ON

    //Build libjasper from source
    BUILD_JASPER:BOOL=OFF

    //Enable Java support
    BUILD_JAVA:BOOL=ON

    //Build libjpeg from source
    BUILD_JPEG:BOOL=ON

    //Build only listed modules (comma-separated, e.g. 'videoio,dnn,ts')
    BUILD_LIST:STRING=

    //Build openexr from source
    BUILD_OPENEXR:BOOL=OFF

    //Enables 'make package_source' command
    BUILD_PACKAGE:BOOL=ON

    //Build performance tests
    BUILD_PERF_TESTS:BOOL=OFF

    //Build libpng from source
    BUILD_PNG:BOOL=ON

    //Force to build libprotobuf from sources
    BUILD_PROTOBUF:BOOL=ON

    //Build shared libraries (.dll/.so) instead of static ones (.lib/.a)
    BUILD_SHARED_LIBS:BOOL=OFF

    //Download and build TBB from source
    BUILD_TBB:BOOL=OFF

    //Build accuracy & regression tests
    BUILD_TESTS:BOOL=OFF

    //Build libtiff from source
    BUILD_TIFF:BOOL=ON

    //Use symlinks instead of files copying during build (and !!INSTALL!!)
    BUILD_USE_SYMLINKS:BOOL=OFF

    //Build WebP from source
    BUILD_WEBP:BOOL=ON

    //Include debug info into release binaries ('OFF' means default
    // settings)
    BUILD_WITH_DEBUG_INFO:BOOL=OFF

    //Enables dynamic linking of IPP (only for standalone IPP)
    BUILD_WITH_DYNAMIC_IPP:BOOL=OFF

    //Build zlib from source
    BUILD_ZLIB:BOOL=ON

    //Build utility applications (used for example to train classifiers)
    BUILD_opencv_apps:BOOL=OFF

    //Include opencv_calib3d module into the OpenCV build
    BUILD_opencv_calib3d:BOOL=OFF

    //Include opencv_core module into the OpenCV build
    BUILD_opencv_core:BOOL=ON

    //Include opencv_dnn module into the OpenCV build
    BUILD_opencv_dnn:BOOL=OFF

    //Include opencv_features2d module into the OpenCV build
    BUILD_opencv_features2d:BOOL=ON

    //Include opencv_flann module into the OpenCV build
    BUILD_opencv_flann:BOOL=ON

    //Include opencv_highgui module into the OpenCV build
    BUILD_opencv_highgui:BOOL=ON

    //Include opencv_imgcodecs module into the OpenCV build
    BUILD_opencv_imgcodecs:BOOL=ON

    //Include opencv_imgproc module into the OpenCV build
    BUILD_opencv_imgproc:BOOL=ON

    //Include opencv_java module into the OpenCV build
    BUILD_opencv_java:BOOL=ON

    //Include opencv_java_bindings_generator module into the OpenCV
    // build
    BUILD_opencv_java_bindings_generator:BOOL=ON

    //Include opencv_js module into the OpenCV build
    BUILD_opencv_js:BOOL=OFF

    //Include opencv_ml module into the OpenCV build
    BUILD_opencv_ml:BOOL=OFF

    //Include opencv_objdetect module into the OpenCV build
    BUILD_opencv_objdetect:BOOL=ON

    //Include opencv_photo module into the OpenCV build
    BUILD_opencv_photo:BOOL=OFF

    //Include opencv_python_bindings_generator module into the OpenCV
    // build
    BUILD_opencv_python_bindings_generator:BOOL=OFF

    //Include opencv_shape module into the OpenCV build
    BUILD_opencv_shape:BOOL=OFF

    //Include opencv_stitching module into the OpenCV build
    BUILD_opencv_stitching:BOOL=OFF

    //Include opencv_superres module into the OpenCV build
    BUILD_opencv_superres:BOOL=OFF

    //Include opencv_ts module into the OpenCV build
    BUILD_opencv_ts:BOOL=OFF

    //Include opencv_video module into the OpenCV build
    BUILD_opencv_video:BOOL=OFF

    //Include opencv_videoio module into the OpenCV build
    BUILD_opencv_videoio:BOOL=OFF

    //Include opencv_videostab module into the OpenCV build
    BUILD_opencv_videostab:BOOL=OFF

    //Include opencv_world module into the OpenCV build
    BUILD_opencv_world:BOOL=OFF

This example minimizes what is build, to what SikuliX needs, and builds the media i/o libs from the bundled sources.
Simply write ON or OFF after the = of an option.

