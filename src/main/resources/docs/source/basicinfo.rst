SikuliX - the basics
====================

SikuliX allows to automate visual workflows
-------------------------------------------

Something like that we do every day sitting in front of our PC:

 - we want to achieve something 
 - we use an application for that (e.g. the browser to access web content)
 - we click some buttons, links or other visuals
 - we wait that the app reacts and shows the expected result on the screen
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
 
 - **Visually** the tool "sees" images (usually retangular pixel areas) on the screen and allows to act on these images using mouse and keyboard simulation. There might be some recorder feture as well with such a tool.
 
SikuliX belongs to the 3rd category and currently does not have a recorder feature. While you work through your workflow you capture some images, that you want to act on or that you expect to appear after an action. These images are used by click and type actions or are used to wait for the screen to be ready for the next action.

SikuliX is a **WYSIWYS-Tool**: What You See Is What You Script.

So again taking the above workflow, now using SikuliX commands:

 | **openApp(someApp)** # we use an application someApp
 | **click(imageButton)** # we click some button
 | **wait(imageExpected1)** # we wait that the app reacts and shows the expected result on the screen
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

In software testing people use tools, to check, wether an application driven by some workflow reveals the expected results. With applications having a visually complex GUI, sooner or later the testers want to check some visual content against their expectation. This might be the presentation of GUI elements on the screen or the visual content of some part of the screen.

SikuliX can be integrated in various ways into such testing environments, either on the scripting level or using the Java based API (where the SikuliX features are implemented). Prominent examples for this approach are RobotFramework or Cucumber.

Also a combination of GUI aware tools and SikuliX are already reality (e.g. together with Selenium for web apps).

The challenge here usually is, that SikuliX's image based features are pixel-aware in the sense, that an image is on the screen exactly pixel-wise or SikuliX fails (with some slight tolerances as long as width and height in pixels match). This usually leads to the neeed to have different image sets for different environments. The version 2 will have some more features, that will make it easier to handle such situations.

Other aspects important for testers:

 - SikuliX needs a real screen running the application under test or at least some equivalent virtual solution 
 - SikuliX is only available on PCs/Workstations running Windows, Mac or Linux and having a Java version 6+
 
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

The actual version 1.1.0 of Sikulix implements a still-there-feature: before searching in the search region, it is first checked, wether the image is still in the same place as at the time of the last search (if the search region contains this last match). On success, this preflight operation usually takes some  milliseconds, which speeds up workflows enormously if they contain repetitive tasks with the same images.

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
 
It is strongly recommended, to have a naming sceme for the image files and to not rely on the basic timestamped image file naming of the SikuliX IDE, which is basically for new users with little programming experience.

Version 2 will have a capturing tool as a standalone app, that supports the basic aspects of image handling:
 - capture and recapture screeshots along a workflow (some kind of recorder)
 - organize your image path
 - organize groups of "same" images, that can be switched depending on environment aspects
 - organize a group of images, that somehow relate to each other and should be found together
 - organize different states of an image (e.g. selected/not selected)
 - optimize screenshots to get the highest possible scores at find
 - some kind of basic support for transparency (e.g. ignore inner part of button) 

SikuliX - system specifics
==========================

.. _SikulixAppSection:

Some general aspects
--------------------

A major aspect of SikuliX is to be available on Windows, Mac and Linux with as little differences as possible. This means, that features will only be added to SikuliX as standard, if they can be made available on all these systems.
Nevertheless it will be possible beginning with version 2, to add extensions or plugins, that might not be available on all systems from the beginning or forever. Version 2 will have a suitable eco-system for that.

Mainly bacause of this major aspect SikuliX is a Java based application or library. Hence the usable artifacts are delivered as jar-files. Were possible the SikuliX IDE is delivered as application (currently Mac only, Windows .exe planned for version 2).

To use the SikuliX features you need a valid Java runtime installation (JRE, preferably the Oracle versions) of at least version 6. SikuliX works with version 7 and 8 too and version 2 will need at least Java 7.

With version 1.1.x, there are still vital parts of SikuliX written in C++, which makes a SikuliX artefact system specific in the end. This currently is supported by an initial setup process, that produces the finally usable artefacts for this system environment.

The only exception is Java programming with some Maven compatible build system, that allows to simply start programming without having done a setup. The needed artifacts for this system are dynamically loaded according to the Maven dependency concept.

Beginning with version 1.1.0 the resulting artefacts (currently sikulix.jar and/or sikulixapi.jar) can be moved around as needed (though it is still recommended to have the SikuliX stuff in one well defined place, to avoid update/upgrade problems). Everything else SikuliX needs during runtime is stored either in the system's temp space or in a special system specific area in the user's home space (see the system specific topics below). Missing or outdated things in these areas are created/recreated at runtime by SikuliX automatically (means: you can delete everything at any time, as long as you keep the jars).

The current layout of this space is as follows (we call it SikulixAppData):

 | ``Extensions`` (place for extensions/Plugins)
 | ``Lib`` (the stuff to support Jython/JRuby usage)
 | ``SikulixDownloads`` (non SikuliX artefacts like Jython, JRuby, Tesseract support, ...)
 | ``SikulixDownloads_TIMESTAMP`` (versioned SikuliX stuff needed for setup)
 | ``SikulixLibs_TIMESTAMP`` (the place for the exported native libraries)
 | ``SikulixSetup`` (optional: used when the setup is run from the project context)
 | ``SikulixStore`` (place for persistent or optional information)
 | ``SikulixTesseract`` (place for language specific tessdata files)

Currently there is no need to step into these folders except for debugging purposes.

SikuliX in the standard does not need any environment settings anymore.

SikuliX on Windows
------------------

The IDE currently (1.1.0) still is only available as jar-file, that can be double-clicked to start it. 

Setup installs a runsikulix.cmd, that can be used to start the IDE from commandline or to run scripts.

For version 2 an application is planned as .exe based on Launch4J.

The SikulixAppData is stored in the folder Sikulix inside the folder the environment variable %APPDATA% points to.

Besides Java there are no prerequisites. All native libraries for 32Bit and/or 64Bit are bundled in the jar-files and exported at runtime as needed. Be aware: the bitness of the native libraries depends on the bitness of the used Java version, which might as well be a 32Bit version on a 64Bit Windows (though not recommended).

SikuliX on Mac
--------------

The IDE is available as Sikulix.app after setup and should be moved to the /Applications folder.

Additionally there is a command script runsikulix to run scripts from commandline (Terminal).

The SikulixAppData folder is here ~/Library/Application Support/Sikulix

Besides Java there are no prerequisites. All native libraries (64Bit, since SikuliX needs OSX 10.6+) are bundled in the jar-files and exported at runtime as needed.

SikuliX on Linux
----------------

The IDE is only available as jar-file, that can be double-clicked to start it (setting the executable bit of the jar-file might be necessary). 

Setup installs a runsikulix command script, that can be used to start the IDE from commandline or to run scripts.

As Java you might use the OpenJDK versions (though the Oracle versions are recommended). A JRE is sufficient, if you do not need a JDK for your own purposes.

Usually on Linux systems SikuliX does not run out of the box, since it is not possible to bundle all native libraries for all possible Linux flavors. It is intended, to have at least a version for the latest Ubuntu systems, that run out of the box.

If setup fails due to library problems, have a look at the setup log to get hints on how to proceed.

About how to get the native libraries working `in case look here <http://www.sikulix.com/quickstart.html>`_.
