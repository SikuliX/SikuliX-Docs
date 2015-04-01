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


