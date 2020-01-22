.. _textandocr:

Working with text and using OCR features
========================================

.. versionadded:: 2.0.2

**Important Note ----------------------------------------------** 

Though the class names ``TextOCR`` (Python scripts) and ``TextRecognizer`` (Java API level) are still supported, they are deprecated. So you should not use them with new works anymore.

In both environments it is now the class name ``OCR``.

On top ``TextOCR.start()`` or ``TextRecognizer.start()`` are no longer needed beforehand (all methods are static). Simply start using the the OCR engin's features like so ``OCR.feature()``.

**--------------------------------------------------------**

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

The height of a capital X in the default font used in Java in the current screen environment is taken as the base for the resize to 30 pixel.

If you have problems with accuarcy, then before fiddling around with the height/size options have a look at the `Lessons learned and BestPractices <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_.

There are functions/methods (classes Region, Image, OCR), that tell The OCR engine, to treat the image as a single line, a single word or even a single character. In some cases their usage might help to get what you expect.

Generally it makes sense, to try with a sample before investing in complex code::

		TODO: Example script to be added

If your interested in the reported accuracy (confidence), you have to use `one of the SikuliX features, that return text matches <>`_::

        match.getScore()
        
which returns a decimal value between 0 and 1 (meant as percentage). Very good values are above 0.95, good values above 0.90.

To get the text in such cases, simply use::

        match.getText()

**Be aware:** Even if a good confidence is reported, there might still be very few errors in the returned text, though the risk is very small. If you need exact results in case you have to intelligently combine the SikuliX and Tesseract features. Even lower confidence values do not mean, that the text is not correctly recognized. Suggestions and contributions are always very welcome.

**Handling options of OCR**

There is one global options set (``OCR.globalOptions()``, that is used if nothing else is said. 

Using ``OCR.options()`` you can create an options set, derived from the initial global options. This can be modified using the setters shown below and later be used with features allowing to specify an option set to use.

As well you can apply the setters to the global options, to run OCR with specific defaults. At any time, you can reset the global options to its initial state using ``OCR.reset()``.

This reports the currently used global options::

            OCR.status()
            
... but not everything is shown yet (under development)::
			
			Global settings OCR.options:
			data = ...some-path.../tessdata
			language(eng) oem(3) psm(3) height(15,1) factor(1,99) dpi(96) LINEAR
			configs: conf1, conf2, ...
			variables: key:value, ...			

The information is usually not relevant, only in cases where you want to report a problem or you are using non-standard SikuliX-OCR-features. More Details you may find below.

For a specific options set (created before using ``OCR.options()``) you can use ``(Java) someOptions.toString()`` to get this information as text (use ``print someOptions`` in scripts).

**Note on running scripts in the IDE**

After a script run, OCR is reset to the defaults of OEM, PSM and text height. If Tesseract variables and/or configs have been set, those are removed as well. So each script run starts with a defined default state of the Tesseract engine.

**BE AWARE** If you want to modify the global options using the following setters, you have to use::

		OCR.globalOptions().setter(value)

**OCR engine mode (OEM)**

The latest version of Tesseract (namely version 4) internally uses a new detection engine (LSTM), that has again raised accuracy and speed. If the corresponding language models are supplied at runtime (which is the case with SikuliX now), then this engine is used as a default (OEM = 3). There should be no need to run another engine mode::

        * OCR Engine modes:
        * 0    Original Tesseract only. TESSERACT_ONLY
        * 1    Cube/LSTM only. LSTM_ONLY
        * 2    Tesseract + Cube/LSTM. TESSERACT_LSTM_COMBINED
        * 3    Default, based on what is available. DEFAULT
        
        OCR.options().oem(value)

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

        OCR.options().language("xxx")
        
Set the language of the text to be read, where xxx is the shorthand for the wanted language (the letters in the filename (Step 3) before the .traineddata).

Another way to set a default language to be used at startup::

        Settings.OcrLanguage = "xxx"
        
This is then recognized with each subsequent script start in the same IDE session (so no need to use start()/setLanguage()).
        
**Have your own Tesseract datapath**
Instead of the above mentioned standard you can have your own folder with all stuff, that is needed by Tesseract at runtime. If you want to do that, simply have:: 

                Settings.OcrDataPath = <some absolute Path>
                
Before starting the Textrecognizer. Take care, that all relevant files are in a subfolder **tessdata**.

This is then recognized with each subsequent script start in the same IDE session (so no need to use start()/setDataPath()).

After having the TextRecognizer started, you can also use::

                OCR.options().dataPath("absolute path")
                
to switch the path dynamically.

**There are many other possibilities to tweak the Tesseract OCR process**

About Tesseract variables, configurations, training and other gory details you have to consult the
`Tesseract documentation <https://github.com/tesseract-ocr/tesseract/wiki/Documentation>`_.

But before you step into Tesseract you should read about `LessonsLearned and BestPractices <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_.

Set a variable as a single Tesseract setting, that controls a specific topic in the OCR process::

        OCR.options().variable(variableKey, variableValue)

Set a configuration which is a file containing a set of variables, that configure the behaviour
of a tailored OCR process. The ``listOfConfigs`` simply is a list of filenames::

        OCR.options().configs(listOfConfigs)

You can set the page segmentation mode (PSM), which tells Tesseract, how to split the given image into rectangles,
that are supposed to contain readable text::

        OCR.options().psm(psm-value)

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
        
Only in special cases there should be a need to use something else than the default (3).

Functions textXXX, findXXX and readXXX in Region, Image or OCR are internally using::

		XXX as Line: psm 7
		XXX as Word: psm 8
		XXX as Char: psm 10
		
So with these convenience functions there is no need to fiddle around with OCR.options beforehand.
