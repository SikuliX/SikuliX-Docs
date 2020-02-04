.. _textandocr:

Working with text and using OCR features
========================================

.. versionadded:: 2.0.2

OCR Summary
-----------

.. note::
	**... important for version 2.0.2+**
	
	Though the class names ``TextOCR`` (Python scripts) and ``TextRecognizer`` (Java API level) are still supported, they are deprecated. So you should not use them with new works anymore.

	In both environments it is now the class name ``OCR``.

	On top ``TextOCR.start()`` or ``TextRecognizer.start()`` are no longer needed beforehand (all methods are static). 

	Simply start using the text/OCR features with :py:class:`Region` or :py:class:`Image` (:ref:`see the summary below <reg_img_features>`).

	In special cases, where you need to tweak the OCR engine, you can use the :py:class:`OCR` features directly (:ref:`see the summary below <ocr_features>`).

SikuliX uses the Java library Tess4j, that allows to use the Tesseract features at the Java level. Internally it depends on Tesseract, 

If you want to know anything about features not mentioned here or supported by SikuliX that are available in Tess4J/Tesseract, you have to dive into the details on the respective home pages of the packages.

 - `Tess4J <http://tess4j.sourceforge.net/>`_
 - `Tesseract <https://github.com/tesseract-ocr/tesseract>`_
 
There are three feature groups (for method specs follow the links):

 - handling general options or settings including those of Tesseract (see below)
 - :ref:`reading the text from a region on screen or from an image (OCR) <reg_img_features>`
 - :ref:`finding the position (Match) of a given text (string/RegEx) in a region on screen or in an image <reg_img_features>`

`Special Information for SikuliX version 2 - Lessons learned and BestPractices (incl. features under developement and/or evaluation) <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_

Accuracy of text recognition (confidence)
-----------------------------------------

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

If your interested in the reported accuracy (confidence), you have to use :ref:`one of the SikuliX features, that return text matches <reg_img_features>`::

        match.getScore()
        
which returns a decimal value between 0 and 1 (meant as percentage). Very good values are above 0.95, good values above 0.90.

To get the text in such cases, simply use::

        match.getText()

.. note::

	Even if a good confidence is reported, there might still be very few errors in the returned text, though the risk is very small. If you need exact results in case you have to intelligently combine the SikuliX and Tesseract features. Even lower confidence values do not mean, that the text is not correctly recognized. Suggestions and contributions are always very welcome.

Handling OCR options
--------------------

There is one **global options set** (:py:class:`OCR.Options`), that is used if nothing else is said.

Using ``myOptions = OCR.Options()`` you can **create a new options set**, derived from the initial global options. This can be modified using the setters shown below (``myOptions.XXX(value)``) and later be used with features allowing to specify an option set to use.

As well you can apply the setters to the global options (``OCR.globalOptions().XXX(value)``), to run OCR with specific defaults. At any time, you can reset the global options to its initial state using :py:meth:`OCR.reset`.

:py:meth:`OCR.status` reports the currently used global options (example for Windows 10 with standard screen settings)::
            			
			Global settings OCR.options:
			data = ...some-path.../tessdata
			language(eng) oem(3) psm(3) height(15,1) factor(1,99) dpi(96) LINEAR
			configs: conf1, conf2, ...
			variables: key:value, ...			

The information is usually not relevant, only in cases where you want to report a problem or you are using non-standard SikuliX-OCR-features. More Details you may find below.

For a specific options set (created before using ``someOptions = OCR.Options()``) you can use ``(Java) someOptions.toString()`` to get this information as text (use ``print someOptions`` in scripts).

The options setters can be chained::

		myOptions = OCR.Options().setter(value).setter(value)...
		
or used alone::

		myOptions = OCR.Options()
		myOptions.setter(value)
		myOptions.setter(value)

.. note::

	**... on running scripts in the IDE**

	After a script run, OCR is reset to the defaults of OEM, PSM and text height. If Tesseract variables and/or configs have been set, those are removed as well. So each script run starts with a defined default state of the Tesseract engine.

OCR engine mode (OEM)
---------------------

The latest version of Tesseract (namely version 4) internally uses a new detection engine (LSTM), that has again raised accuracy and speed. If the corresponding language models are supplied at runtime (which is the case with SikuliX now), then this engine is used as a default (OEM = 3). 

see :py:meth:`oem`

Normally there should be no need to run another engine mode.

OCR page segmentation mode(PSM)
-------------------------------

You can set the page segmentation mode (PSM), which tells Tesseract, how to split the given image into rectangles,
that are supposed to contain readable text.

see :py:meth:`psm`
        
Only in special cases there should be a need to use something else than the default (3).

Switch to another language
--------------------------
 
In the standard SikuliX runs the text features with the english language set, which is bundled with SikuliX. It is possible to add more languages to your SikuliX setup and switch between the installed languages at runtime.

These are the steps to switch to another language than the standard english (eng):

**Step 1**: Find the folder ``SikulixTesseract/tessdata`` in your SikuliX <app-data> folder (see docs)

**Step 2**: Download the languages needed from `Tesseract languages <https://github.com/tesseract-ocr/tessdata>`_
(only the files with .traineddata)

For SikuliX version 2.0.x+ we use the files for Tesseract 4 (preferably those from **tessdata_fast**)

For earlier Versions up to 1.1.3 use the files for Tesseract 3 (no longer supported).

**Step 3**: Put the .traineddata files into the tessdata folder (Step 1)

In your script, that should use the language, say before using an OCR feature::

        OCR.globalOptions().language()
        
Another way to set a default language to be used after startup globally::

        Settings.OcrLanguage = "xxx"
        
This is then recognized with each subsequent script start in the same IDE session.
        
Have your own Tesseract datapath
--------------------------------

Instead of the above mentioned standard you can have your own folder with all stuff, that is needed by Tesseract at runtime. If you want to do that, simply have:: 

                Settings.OcrDataPath = <some absolute Path>
                
Before starting the Textrecognizer. Take care, that all relevant files are in a subfolder **tessdata**.

This is then recognized with each subsequent script start in the same IDE session.

Use :py:meth:`OCR.Options.dataPath` to switch the path dynamically.

Other possibilities to tweak the Tesseract OCR process
------------------------------------------------------

About Tesseract variables, configurations, training and other gory details you have to consult the
`Tesseract documentation <https://github.com/tesseract-ocr/tesseract/wiki/Documentation>`_.

But before you step into Tesseract you should read about `LessonsLearned and BestPractices <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_.

Set a variable as a single Tesseract setting, that controls a specific topic in the OCR process :py:meth:`OCR.Options.variable`

Set a configuration which is a file containing a set of variables, that configure the behaviour
of a tailored OCR process: :py:meth:`OCR.Options.configs`.

The Text and OCR features in Detail (Class OCR)
-----------------------------------------------

.. _ocr_features:

.. include:: classDoc_OCR.txt

.. _reg_img_features:

**to be written**
