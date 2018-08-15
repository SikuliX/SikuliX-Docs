New in version 1.1.4
====================

Version 1.1.4 has some features only planned for version 2, which seems to need some more time to get ready ;-)

A summary for now (until released):

 - **Java-only** most of the stuff implemented in C++ up to version 1.1.3 is now at Java level.
 
 - **OpenCV 3.4** the latest OpenCV version will now be used always. The needed stuff (Java classes and native library
   for Java support will be bundled or at least available for download).
 
 - **Text and OCR features** are now implemented using the Java library Tess4J (current latest version based on
   Tesseract 3.0.5). All Tesseract options will be available at the Java API level.
   The Text/OCR features are completely revised and augmented.
 
 - **Transparency** images with transparent areas (called masks) are now supported for search (ignoring the parts
   of the rectangles being transparent). Features will be available to create masks and masked images.
 
**1.1.4 (as nightly build) will be available earliest August 20, 2018**

Revision of the find API
------------------------

To support all features both for image and text search, the text search now has its own function set,
whose behavior is the same as for image search, except, that a given text is directly taken as the text to be
searched and not checked for a possible image file.

The old behavior of ``find(someText)`` is now switched off in the standard, but can be switched on
by intention (``Settings.SwitchToText``). But if switched on, the ``ImageMissing`` feature is not available.
In doubt it is recommended, to change the function calls to their text equivalents, if text search is the intention.

**How to read the following tables:**

In the 1st column you have the function names to be used as ``function(parameter1, parameter2, ...)``. About more
details :ref:`see the function description itself<FindinginsideaRegionandWaitingforaVisualEvent>`.

In all cases, these functions return a :py:class:`Match` object as result, which is ``null/None`` if not found.

 - **repeats-search** ``yes`` means, that the search is continued until either the image/text appears in the search region
  or until the standard/given waiting time is exceeded (which means a FindFailed). ``no`` means, that only one search is
  performed with the content of the search region at that moment (meaning either found or not found)

 - **throws-FindFailed** ``yes`` means, that in case of not found (also for elapsed waiting time), a FindFailed
   exception is thrown, which leads to script/program termination, if not handled somehow. ``no`` means, that the function
   silently returns ``null/None``, which must be checked if relevant.
   :ref:`About handling FindFailed cases read more here<ExceptionFindFailed>`.

==================   ==================   =====================
**image-function**   **repeats-search**   **throws-FindFailed**
  find                  no                  yes
  wait                  yes                 yes
  has                   no                  no
  exists                yes                 no
==================   ==================   =====================

==================   ==================   =====================
**text-function**    **repeats-search**   **throws-FindFailed**
  findText             no                   yes
  waitText             yes                  yes
  hasText              no                   no
  existsText           yes                  no
==================   ==================   =====================
