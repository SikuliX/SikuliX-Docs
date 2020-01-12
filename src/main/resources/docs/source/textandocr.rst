.. _textandocr:

Working with text and using OCR features
========================================

`Special Info for SikuliX version 2 (incl. features under developement and/or evaluation) <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_

**Switch to another language** 
 
These are the steps to switch to another language than the standard english (eng):

Step 1: Find the folder ``SikulixTesseract/tessdata`` in your SikuliX <app-data> folder (see docs)

Step 2: Download the languages needed from `Tesseract languages <https://github.com/tesseract-ocr/tessdata>`_
(only the files with .traineddata)

For SikuliX version 2.0.x+ we use the files for Tesseract 4 (preferably those from **tessdata_fast**)

For earlier Versions up to 1.1.3 use the files for Tesseract 3 (no longer supported).

Step 3: Put the .traineddata files into the tessdata folder (Step 1)

In your script say before using OCR features::

        # in a script
        tr = TextOCR.start()

        // in Java
        TextRecognizer tr = TextRecognizer.start()

Set the language of the text to be read:
  where xxx is the shorthand for the wanted language (the letters in the filename (Step 3) before the .traineddata)::

        tr.setLanguage("xxx")
        
**Have your own Tesseract datapath**
Instead of the above mentioned standard you can have your own folder with all stuff, that is needed by Tesseract at runtime. If you want to do that, simply have 

                Settings.OcrDataPath = <some absolute Path>
                
Before starting the Textrecognizer. Take care, that all relevant files are in a subfolder **tessdata**.

**There are many other possibilities to tweak the Tesseract OCR process**

About Tesseract variables, configurations, training and other gory details you have to consult the
`Tesseract documentation <https://github.com/tesseract-ocr/tesseract/wiki/Documentation>`_.

But before you step into Tesseract you should read about **`LessonsLearned and BestPractices <https://github.com/RaiMan/SikuliX1/wiki/How-to-get-the-best-from-OCR-and-text-features>`_**.

Set a variable as a single Tesseract setting, that controls a specific topic in the OCR process::

        tr.setVariable(variableKey, variableValue)

Set a configuration which is a file containing a set of variables, that configure the behaviour
of a tailored OCR process. The ``listOfConfigs`` simply is a list of filenames::

        tr.setConfigs(listOfConfigs)

You can set the page segmentation mode (PSM), which tells Tesseract, how to split the given image into rectangles,
that are supposed to contain readable text::

        tr.setPSM(psm-value)

        * Page segmentation modes:
        *   0    Orientation and script detection (OSD) only.
        *   1    Automatic page segmentation with OSD.
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
        *  12    Sparse text with OSD.
        *  13    Raw line. Treat the image as a single text line, bypassing hacks that are Tesseract-specific.

