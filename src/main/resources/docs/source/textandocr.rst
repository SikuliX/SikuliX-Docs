.. _textandocr:

Working with text and using OCR features
========================================

These are the steps to switch to another language than the standard english (eng):

1. find the folder ``SikulixTesseract/tessdata`` in your SikuliX <app-data> folder (see docs)

2. download the languages needed from
`Tesseract languages version 3 <https://github.com/tesseract-ocr/tessdata/tree/3.04.00>`_ (only the files with .traineddata)

3. put the .traineddata files into the tessdata folder (step 1.)

4. in your script say before using OCR features::

        tr = TextRecognizer.start()
        tr.setLanguage("xxx")

where xxx is the shorthand for the wanted language (the letters in the filename (step 3.) before the .traineddata)
