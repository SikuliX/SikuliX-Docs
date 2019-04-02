.. _Basics:

SikuliX - the basics
====================

SikuliX allows one to automate visual workflows
-------------------------------------------

Something like that we do every day sitting in front of our PC:

 - we want to achieve something 
 - we use an application for that (e.g. the browser to access web content)
 - we click some buttons, links or other visuals
 - we wait for the app to react and shows the expected result on the screen
 - we fill in some text somewhere and press some functional keys like TAB or ENTER
 - again we wait for some expected reaction or result
 - we click ...
 - we wait ...
 - we type ...
 - we wait ...
 - we ...
 - ...
 
This we call a **visual workflow**.
 
Some of these workflows everyone repeats more or less often and from time to time we ask ourselves: can that be automated?

There are many tools out there, that can be used for such a workflow automation and who ever made some experiments in this area will have made his experiences and might have favorite tools for automation. 

There are basically 3 categories of tools:

 - **Recorder** while you manually work along your workflow, a recorder tracks your mouse and keyboard actions. After stopping the recording, you might playback (autorun your worflow). The recordings can usually be edited and augmented with additional features.
 
 - **GUI aware** the tool allows to programmatically operate on GUI elements like buttons. This is based on the knowledge of internal structures and names of the GUI elements and their features. Some of these tools also have a recording feature.
 
 - **Visually** the tool "sees" images (usually retangular pixel areas) on the screen and allows one to act on these images using mouse and keyboard simulation. There might be some recorder feature as well with such a tool.
 
SikuliX belongs to the 3rd category and currently does not have a recorder feature. While you work through your workflow you capture some images, that you want to act on or that you expect to appear after an action. These images are used by click and type actions or are used to wait for the screen to be ready for the next action.

SikuliX is a **WYSIWYS-Tool**: What You See Is What You Script.

So again taking the above workflow, now using SikuliX commands:

 | **openApp(someApp)** # we use an application someApp
 | **click(imageButton)** # we click some button
 | **wait(imageExpected1)** # we wait for the app to react and show the expected result on the screen
 | **type("some text"); type(Key.ENTER)** # we fill in some text and press ENTER
 | **wait(imageExpected2)** # again we wait for some expected reaction or result
 | **click(...)** # we click ...
 | **wait(...)** # we wait ...
 | **type(...)** # we type ...
 | **wait(...)** # we wait ...
 | ...

Using the SikuliX IDE, you can setup and maintain such visual workflows including capturing and organizing the needed images. Besides knowing the options of the basic commands, you do not need any programming or scripting knowledge. Only in the moment, you want to optimize, repeat or augment such a basic linear workflow, you have to dive deeper into the scripting language of your choice (supported in the IDE: Python, Ruby, JavaScript).

SikuliX can be used for visual testing
--------------------------------------

In software testing people use tools, to check, whether an application driven by some workflow reveals the expected results. With applications having a visually complex GUI, sooner or later the testers want to check some visual content against their expectation. This might be the presentation of GUI elements on the screen or the visual content of some part of the screen.

SikuliX can be integrated in various ways into such testing environments, either on the scripting level or using the Java based API (where the SikuliX features are implemented). Prominent examples for this approach are RobotFramework or Cucumber.

Also a combination of GUI aware tools and SikuliX are already reality (e.g. together with Selenium for web apps).

The challenge here usually is, that SikuliX's image based features are pixel-aware in the sense, that an image is on the screen exactly pixel-wise or SikuliX fails (with some slight tolerances as long as width and height in pixels match). This usually leads to the neeed to have different image sets for different environments. The version 2 will have some more features, that will make it easier to handle such situations.

Other aspects important for testers:

 - SikuliX needs a real screen running the application under test or at least some equivalent virtual solution 
 - SikuliX is only available on PCs/Workstations running Windows, Mac or Linux and having a Java version 6+
 
.. _BasicsFind:
 
SikuliX - how does it find images on the screen?
------------------------------------------------

SikuliX uses the `OpenCV <http://opencv.org/>`_ package for finding an image on the screen.

The SikuliX feature is based on OpenCV's method matchTemplate(), which is rather good explained on `this example page <http://docs.opencv.org/doc/tutorials/imgproc/histograms/template_matching/template_matching.html>`_. If you are not familiar with how it works, you should just have a look there and then come back to here and read further.

A basic feature in Sikulix is to wait for an image to appear in a given region:

 | # some top left part of the screen
 | aRegion = Region(0, 0, 500, 500) 
 | # a png image file on the file system
 | # this is the image we want to look for in the given Region
 | aImage = "someImage.png" 
 | # search and get the result
 | aMatch = aRegion.find(aImage)

To not make it too complicated here, I do not talk about how you create the aImage - we just assume it is there and accessible.

The matchTemplate() expects an even sized or larger image (base), where the given image (target) should be searched. To prepare that, we internally make a screenshot (using Java Robot class) of the screen area defined by the given aRegion. This now is the base image and held in memory. The target image is also created as in memory image read form the image file. Both images then are converted to the needed OpenCV objects (CVMat).

Now we run the matchTemplate() function and get a matrix in the size of the base image, that contains for each pixel a similarity score for the target image compared pixel by pixel with it's top left corner at this pixel location. If this is not clear here now, go back to the above example and try to understand.
The score values at each pixel location vary between 0.0 and 1.0: the lower the value, the lower the probability, that the area with it's top left corner at this pixel location contains the target image.
Score values above 0.7 - 0.8 signal a high probability, that the image is here. 

In the next step, we use another OpenCV method, to get the relevant max value (result score) from the mentioned result matrix, meaning that we are looking for a pixel, that most probable is the top left corner of the target image in the base image.

If nothing else is said, only a result score > 0.7 is taken as found. The other values will signal a FindFailed exception. Depending on various aspects of the target image (mainly how much even background towards the edges in contained in the target image), one usually get result scores > 0.8 or even 0.9. If one follows SikuliX's recommendation how to create target images, one should in most cases get result scores >0.95 or even >0.99 (internally taken as exact match with 1.0). 

If the result score is accepted as found, in the next step we create a match object, that denotes the region on the screen, that most probably contains the image (aMatch in the above snippet).

If the image is not found (result score not acceptable), we either terminate the search operation signalling failed or start a new search with a new screenshot of the given region. This is repeated until the image is either found or the given or implicit waiting time (3 seconds in the standard) has elapsed, which also results in a FindFailed signal. The rate of this repetition can be specified, to reduce the cpu usage by SikuliX, since the search process is pure number crunching.

A word on elapsed time for search operations: The larger the base image the longer the search. The smaller the size difference of the 2 images, the faster. On modern systems with large monitors searching a small to medium sized image (up to 10.000 pixels), the elapsed time might be between 0.5 and 1 second or even more. The usual approach, to reduce search time is to reduce the search region as much as possible to the area, one expects the target image to appear. Small images of some 10 pixels in search regions of some 1000 pixels are found within some 10 milliseconds or even faster.

Sikulix implements a still-there-feature: before searching in the search region, it is first checked, whether the image is still in the same place as at the time of the last search (if the search region contains this last match). On success, this preflight operation usually takes some  milliseconds, which speeds up workflows if they contain repetitive tasks with the same images.

Not knowing the magic behind SikuliX's search feature and the matchTemplate() function, people always wonder, why images showing up multiple times on the screen, are not found in some regular order (e.g. top left to bottom right). That this is not the case is caused by the implementation of the matchTemplate() function as some statistical numeric matrix calculations. So never expect SikuliX to return the top left appearance of a visual being more than once on the screen at time of search. The result is not predictable in this sense.

If you want to find a specific item of these multiple occurrences, you have to restrict the search region, so that only the one you are looking for is found.

For cases where this is not suitable or if you want to cycle through all appearances, we have the findAll() method, that returns a list of matches in decreasing result score order. You might work through this list according to their position on the screen by using their (x,y) top left corner coordinates. findAll internally evaluates the search result matrix, by repetitively  looking for the next max value after having "switched off" some area around the last max.

SikuliX - handling of images
----------------------------

To use images with the features of SikuliX like click(someImage), you need to store these images as image files preferably in the PNG format (someImage.png) somewhere on the file system or somewhere in the internet.

An image in this sense is some rectengular pixel area taken from the screen (captured or made a screenshot - with Sikuli we use the verb capture as the process of taking the image and save it in an image file and the name screenshot for the artifact "visual object = image").

Capturing is supported by the IDE or programmatically via the respective SikuliX features. You might use any capture tool instead to get your images (preferably in PNG format).

To load the images SikuliX has 2 principles:

 - **bundle path**: the images are stored together with the script file (.py for Python, .rb for Ruby, .js for JavaScript) in a folder named someScript.sikuli, where the scriptfile must be named the same as the folder (e.g. someScript.py). This all is automatically assured, when working with the SikuliX IDE.
 
 - **image path**: additionally SikuliX supports a list of places as an image path. Possible places are folders in the file system, folders in a jar-file and folders somewhere in the internet. There are functions available to manage your own image path. When an image has to be loaded (exception: the absolute path is given), the places are sequentially checked for the existence of the image. The first matching place wins.
 
It is strongly recommended, to have a naming scheme for the image files and to not rely on the basic timestamped image file naming of the SikuliX IDE, which is basically for new users with little programming experience.

It is planned to have a capturing tool as a standalone app, that supports the basic aspects of image handling:
 - capture and recapture screeshots along a workflow (some kind of recorder)
 - organize your image path
 - organize groups of "same" images, that can be switched depending on environment aspects
 - organize a group of images, that somehow relate to each other and should be found together
 - organize different states of an image (e.g. selected/not selected)
 - optimize screenshots to get the highest possible scores at find

SikuliX - system specifics
==========================

.. _SikulixAppData:

Some general aspects
--------------------

**This is valid for version 1.1.4+ (prior versions are no longer supprted)**

Might be a good idea to first read this `SikuliX 1.1.x Quickstart <http://sikulix.com/quickstart/>`_.

**SikuliX does only support 64-Bit Systems.**

A major aspect of SikuliX is to be available on Windows, Mac and Linux with as little differences as possible. This means, that features will only be added to SikuliX as standard, if they can be made available on all these systems. Nevertheless it is possible to :ref:`add extensions or plugins<sxExtensions>`, that might not be available for all systems.

SikuliX is a Java based application or library. Hence the usable artifacts are delivered as jar-files.

To use the SikuliX features you need a valid Java runtime installation of at least version 8 (Oracle JRE or JDK). Java 9+ is supported with version 1.1.4+ (preferably OpenJDK latest, version 12 as of March 2019).

The Sikulix artefacts ``sikulix.jar`` and/or ``sikulixapi.jar`` can be moved around as needed (though it is still recommended to have the SikuliX stuff in one well defined place, to avoid update/upgrade problems). Everything else SikuliX needs during runtime is stored either in the system's temp space or in a special system specific area in the user's home space (see the system specific topics below). Missing or outdated things in these areas are created/recreated at runtime by SikuliX automatically (means: you can delete everything at any time, as long as you keep the jars).

For the same reason when working with other IDE's, it is strongly recommended to work with links/pointers to ``sikulixapi.jar`` and in case the ``Lib folder (Python stuff)`` and not having copies in different places.

The current layout of this space is as follows (we call it **SikulixAppData**):

 | ``Extensions`` (place for extensions/Plugins)
 | ``Lib`` (the stuff to support Jython/JRuby usage)
 | ``SikulixDownloads`` (non SikuliX artefacts like Jython, JRuby, Tesseract support, ...)
 | ``SikulixLibs`` (folder containing system specific native libraries)
 | ``SikulixStore`` (place for persistent or optional information)
 | ``SikulixTesseract`` (place for language specific tessdata files)

Currently there is no need to step into these folders except for debugging purposes. In case of startup problems, it might help to delete ``SikulixAppData`` and restart.

SikuliX in the standard does not need any environment settings anymore. It is a good idea, to remove anything from your environment, that points to stuff from prior versions.

SikuliX on Windows
------------------

The IDE is only available as jar-file, that can be double-clicked to start it.

Usage in a command window: ``java -jar <path-to>/sikulix.jar`` (`for options see<RunningScriptsFromCommandLine>`)

The ``SikulixAppData`` is stored in the folder ``Sikulix`` inside the folder the environment variable ``%APPDATA%`` points to.

Besides Java there are no prerequisites. All native libraries are bundled in the jar-files and exported at runtime as needed.

SikuliX on Mac
--------------

The IDE is only available as jar-file, that can be double-clicked to start it.

Usage in a Terminal window: ``java -jar <path-to>/sikulix.jar`` (`for options see<RunningScriptsFromCommandLine>`)

The ``SikulixAppData`` folder is here ``~/Library/Application Support/Sikulix``

Besides Java there are no prerequisites. All native libraries are bundled in the jar-files and exported at runtime as needed.

SikuliX on Linux
----------------

As Java you should use the OpenJDK versions (or the Oracle versions where appropriate).

The IDE is only available as jar-file, that can be double-clicked to start it (setting the executable bit of the jar-file might be necessary).

Usage in a Terminal window: ``java -jar <path-to>/sikulix.jar`` (`for options see<RunningScriptsFromCommandLine>`)

On Linux Systems SikuliX cannot be used out of the box. For the details see :ref:`New in 1.1.4 <NewIn114>`.

One has to provide the libraries **OpenCV and Tesseract** and additionally the packages **wmctrl and xdotool**, when you want to use the App class features.

**Special information on the bundled libJXGrabKey.so**

At least on newer Ubuntu versions ldd -r reports unresolved symbols pthread… It seems, that this can be ignored, since JXGrabKey works.
If you get problems, that are related to JXGrabKey, you might have to build from the sources and provide the ready built library in the ``~/.Sikulix/SikulixLibs`` folder.

SikuliX Offline Setup
---------------------

**BE AWARE** This is only for **SikuliX 1.1.3** (but no longer supported nor necessary with 1.1.4+).

In any case, you need a valid **sikulixsetup-1.1.3.jar** in a folder of your choice. 

If during setup for some reason internal downloads are blocked on your system, you might try an **Offline Setup** according to the following steps.

 - optional: make a folder Downloads in the folder intended to contain SikuliX
 - manually download the respective packages (see below, links should start the download)
 - optional: put them in the setup Downloads folder
 - run setup and select the respective options
 - when asked, confirm the usage of the already downloaded packages

You might as well store the manually downloaded artefacts in the same folder as the **sikulixsetup....jar**. 
During setup the artefacts will be moved to the SikuliX local application data folder anyways.

**Packages to download manually for version 1.1.3**:

to get the **IDE (option 1)** download from OSSRH (`sikulixsetupIDE....jar <https://oss.sonatype.org/content/groups/public/com/sikulix/sikulixsetupIDE/1.1.3-SNAPSHOT/sikulixsetupIDE-1.1.3-20180711.082119-40-forsetup.jar>`_)
 
to get the **Java API (option 1 and 2)** download from OSSRH (`sikulixsetupAPI....jar <https://oss.sonatype.org/content/groups/public/com/sikulix/sikulixsetupAPI/1.1.3-SNAPSHOT/sikulixsetupAPI-1.1.3-20180711.082046-40-forsetup.jar>`_)

**native libraries for version 1.1.3**:

for **Windows** download from MavenCentral (`sikulixlibswin....jar <http://repo1.maven.org/maven2/com/sikulix/sikulixlibswin/1.1.1/sikulixlibswin-1.1.1.jar>`_)

for **Mac** download from MavenCentral (`sikulixlibsmac....jar <http://repo1.maven.org/maven2/com/sikulix/sikulixlibsmac/1.1.1/sikulixlibsmac-1.1.1.jar>`_)

for **Linux/Unix** download from MavenCentral (`sikulixlibslux....jar <http://repo1.maven.org/maven2/com/sikulix/sikulixlibslux/1.1.1/sikulixlibslux-1.1.1.jar>`_)
  
**Packages to download manually for version 1.1.2 and later** 

to get **Jython 2.7** (for option 1) download from MavenCentral (`jython-standalone-2.7.....jar <https://repo1.maven.org/maven2/org/python/jython-standalone/2.7.1/jython-standalone-2.7.1.jar>`_)

to get **JRuby** (for option 1) download from MavenCentral (`jruby-complete-9.x ..... jar <https://repo1.maven.org/maven2/org/jruby/jruby-complete/9.2.0.0/jruby-complete-9.2.0.0.jar>`_)
 
**For the text features (based on Tesseract)** the files supporting english language are bundled with sikulixapi.jar. 

If you need the files for other languages: `here they are <https://github.com/tesseract-ocr/tessdata>`_.
