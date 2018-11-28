.. _textandocr:

Working with text and using OCR features
========================================

These are the steps to **switch to another language** than the standard english (eng):

Find the folder ``SikulixTesseract/tessdata`` in your SikuliX <app-data> folder (see docs)

Download the languages needed from `Tesseract languages version 3 <https://github.com/tesseract-ocr/tessdata/tree/3.04.00>`_
(only the files with .traineddata)

Put the .traineddata files into the tessdata folder (step 1.)

In your script say before using OCR features::

        # in a script
        tr = TextOCR.start()

        // in Java
        TextRecognizer tr = TextRecognizer.start()

Set the language of the text to be read:
  where xxx is the shorthand for the wanted language (the letters in the filename (step 3.) before the .traineddata)::

        tr.setLanguage("xxx")

**There are many other possibilities to tweak the Tesseract OCR process**

About Tesseract variables, configurations, training and other gory details you have to consult the
`Tesseract documentation <https://github.com/tesseract-ocr/tesseract/wiki/Documentation>`_.

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

