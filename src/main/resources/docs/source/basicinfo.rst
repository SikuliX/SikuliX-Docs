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

 - **openApp(someApp)** # we use an application someApp
 - **click(imageButton)** # we click some button
 - **wait(imageExpected1)** # we wait that the app reacts and shows the expected result on the screen
 - **type("some text"); type(Key.ENTER)** # we fill in some text and press ENTER
 - **wait(imageExpected2)** # again we wait for some expected reaction or result
 - **click(...)** # we click ...
 - **wait(...)** # we wait ...
 - **type(...)** we type ...
 - **wait(...)** # we wait ...
 - ...

Using the SikuliX IDE, you can setup and maintain such visual workflows including capturing and organizing the needed images. Besides knowing the options of the basic commands, you do not need any programming or scripting knowledge. Only in the moment, you want to optimize, repeat or augment such a basic linear workflow, you have to dive deeper into the scripting language of your choice (supported in the IDE: Python, Ruby, JavaScript).
