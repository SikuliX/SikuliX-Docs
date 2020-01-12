.. _textandocr:

Working with text and using OCR features
========================================

.. versionadded:: 2.0.2

SikuliX uses the Java library Tess4j, that allows to use the Tesseract features at the Java level. Internally it depends on Tesseract, 

If you want to know anything about features not mentioned here or supported by SikuliX that are available in Tess4J/Tesseract, you have to dive into the details on the respective home pages of the packages.

 - `Tess4J <http://tess4j.sourceforge.net/>`_
 - `Tesseract <https://github.com/tesseract-ocr/tesseract>`_
 
There are three feature groups (for method specs follow the links):

 - handling general options or settings including those of Tesseract (see below)
 - `reading the text from a region on a screen or from an image (OCR) <https://sikulix-2014.readthedocs.io/en/latest/region.html#extracting-text-from-a-region>`_
 - `finding the position (Match) of a given text (string/RegEx) in a region on a screen or in an image <>`_

`Special Information for SikuliX version 2 - Lessons learned and BestPractices (incl. features under developement and/or evaluation) <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_

**Accuracy of text recognition (confidence)**

According to the recommendations of Tesseract and experiences found in the net, SikuliX does some optimization of the images before handing over to OCR:

 - convert to grayscale
 - do some edge sharpening
 - revert images with light text on dark background to black on white
 - resize the image to an optimum size
 
On the latter (optimum size) some comments:
 - with Tessreact 3 the recommendation was to rezize to an image of 200 - 300 dpi
 - with Tesseract 4 the best practice seems to be: resize to a size, where the average height of a capital letter (preferably the X) has a height of about 30 pixel
 
Both variants have drawbacks, but the pixel approach seems to be the most promising (own tests and experiences from the net). The dpi approach works too with slightly lower accuarcy, but is too dependent on the current screen settings, which in turn are differently handled by Java in different system environments.

To not make usage too complicated in the average, SikuliX comes with the following default:

The height of a capital X in the default font in the current screen environment is taken as the base for the resize to 30 pixel.

In case you might change this setting::

        tr.setUppercaseXHeight(value)
        
where value must be greater than 7 and should be the height in pixels of the capital letter X in the respective font as shown on the screen or in the image.

There is another option, that might be used instead::

        tr.setFontSize(value)
        
where value must be greater than seven and be the font size in dpi of the standard font used by Java in your environment.

If you have problems with accuarcy, then before fiddling around with the height/size options have a look at the `Lessons learned and BestPractices <>`_.

If your interested in the reported accuracy (confidence), you have to use one of the SikuliX features, that return text matches::

        match.getScore()
        
which returns a decimal value between 0 and 100 (meant as percentage). Very good values are above 95, good values above 90.

Be aware: even if a good confidence is reported, there might still be very few errors in the returned text, though the risk is very small. If you need exact results in case you have to intelligently combine the SikuliX and Tesseract features. Suggestions and contributions are always very welcome.

**Getting access to the options/settings of OCR**

Most of the OCR and text find features work with standard settings out of the box. If you need to tweak the OCR process, you have to start the engine (TextRecognizer) beforehand::

        # in a script
        tr = TextOCR.start()

        // in Java
        TextRecognizer tr = TextRecognizer.start()
        
After that you have access to the settings/options via setters/getters like so:

        tr.setSomeOption(someValue)
        tr.getSomeOption()
        
For details see below.

**Note on running scripts in the IDE**

After a script run, a started Textrecognizer is reset to the defaults of OEM, PSM and text height. If Tesseract variables and/or configs have been set, it is even stopped and started again. So each script run starts with a defined default state of the engine.

This reports the current settings of the engine:

            TextOCR.start().status()
            
... but not everything is shown yet (under development).

**OCR engine mode (OEM)**

The latest version of Tesseract (namely version 4) internally uses a new detection engine (LSTM), that has again raised accuracy and speed. If the corresponding language models are supplied at runtime (which is the case with SikuliX now), then this engine is used as a default (OEM = 3). There should be no need to run another engine mode::

        * OCR Engine modes:
        * 0    Original Tesseract only. TESSERACT_ONLY
        * 1    Cube/LSTM only. LSTM_ONLY
        * 2    Tesseract + Cube/LSTM. TESSERACT_LSTM_COMBINED
        * 3    Default, based on what is available. DEFAULT
        
        tr.setOEM(value)

**Switch to another language** 
 
In the standard SikuliX runs the text features with the english language set, which is bundled with SikuliX. It is possible to add more languages to your SikuliX setup and switch between the installed languages at runtime.

These are the steps to switch to another language than the standard english (eng):

Step 1: Find the folder ``SikulixTesseract/tessdata`` in your SikuliX <app-data> folder (see docs)

Step 2: Download the languages needed from `Tesseract languages <https://github.com/tesseract-ocr/tessdata>`_
(only the files with .traineddata)

For SikuliX version 2.0.x+ we use the files for Tesseract 4 (preferably those from **tessdata_fast**)

For earlier Versions up to 1.1.3 use the files for Tesseract 3 (no longer supported).

Step 3: Put the .traineddata files into the tessdata folder (Step 1)

In your script/program start the engine and say before using OCR features::

        tr.setLanguage("xxx")
        
Set the language of the text to be read, where xxx is the shorthand for the wanted language (the letters in the filename (Step 3) before the .traineddata).

Another way to set a default language to be used at startup::

        Settings.OcrLanguage = "xxx"
        
This is then recognized with each subsequent script start in the same IDE session (so no need to use start()/setLanguage()).
        
**Have your own Tesseract datapath**
Instead of the above mentioned standard you can have your own folder with all stuff, that is needed by Tesseract at runtime. If you want to do that, simply have 

                Settings.OcrDataPath = <some absolute Path>
                
Before starting the Textrecognizer. Take care, that all relevant files are in a subfolder **tessdata**.

This is then recognized with each subsequent script start in the same IDE session (so no need to use start()/setDataPath()).

After having the TextRecognizer started, you can also use::

                tr.setDataPath("absolute path")
                
to switch the path dynamically.

**There are many other possibilities to tweak the Tesseract OCR process**

About Tesseract variables, configurations, training and other gory details you have to consult the
`Tesseract documentation <https://github.com/tesseract-ocr/tesseract/wiki/Documentation>`_.

But before you step into Tesseract you should read about `LessonsLearned and BestPractices <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_.

Set a variable as a single Tesseract setting, that controls a specific topic in the OCR process::

        tr.setVariable(variableKey, variableValue)

Set a configuration which is a file containing a set of variables, that configure the behaviour
of a tailored OCR process. The ``listOfConfigs`` simply is a list of filenames::

        tr.setConfigs(listOfConfigs)

You can set the page segmentation mode (PSM), which tells Tesseract, how to split the given image into rectangles,
that are supposed to contain readable text::

        tr.setPSM(psm-value)

        * Page segmentation modes:
        *   0    Orientation and script detection (OSD) only. (needs osd.traineddata)
        *   1    Automatic page segmentation with OSD.  (needs osd.traineddata)
        *   2    Automatic page segmentation, but no OSD, or OCR.
        *   3    Fully automatic page segmentation, but no OSD. (Default)
        *   4    Assume a single column of text of variable sizes.
        *   5    Assume a single uniform block of vertically aligned text.
        *   6    Assume a single uniform block of text.
        *   7    Treat the image as a single text line.
        *   8    Treat the image as a single word.
        *   9    Treat the image as a single word in a circle.
        *  10    Treat the image as a single character.
        *  11    Sparse text. Find as much text as possible in no particular order.
        *  12    Sparse text with OSD.  (needs osd.traineddata)
        *  13    Raw line. Treat the image as a single text line, bypassing hacks that are Tesseract-specific.
        
Only in specuial cases there should be a need to use something else than the default (3).
