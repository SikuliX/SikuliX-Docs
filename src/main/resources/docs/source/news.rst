New in version 1.1.4
====================

Version 1.1.4 has some features only planned for version 2, which seems to need some more time to get ready ;-)

A summary for now (until released):

 - **Java-only** most of the stuff implemented in C++ is now at Java level. 
 
 - **OpenCV 3.4** the latest OpenCV version will now be used always. The needed stuff (Java classes and native library for Java support will be bundled or at least available for download).
 
 - **Text and OCR features** are now implemented using the Java library Tess4J (current latest version based on Tesseract 3.0.5). All Tesseract options will be available at the Java API level. The Text/OCR features are completely revised and augmented.
 
 - **Transparency** images with transparent areas (called masks) are now supported for search (ignoring the parts of the rectangles being transparent). Features will be available to create masks and masked images.
 
**1.1.4 (as nightly build) will be available latest August 17, 2018**

========   ==============   =================
function   repeats-search   throws-FindFailed
--------   --------------   -----------------
find       no               yes
wait       yes              yes
--------   --------------   -----------------
