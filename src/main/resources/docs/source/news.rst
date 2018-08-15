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

The equivalent functions that definitely look for the given text are named ``xxxText``.
There is also a shortcut set named only ``xxxT``. This might be helpful, when migrating to the new function set
for text search: use the xxxT version for replacements and xxxText for fresh implementations - or vice versa.

**Be aware** other than the image search functions, the text search functions search from top left to bottom right.
So if there is more than one possible match in a region, always the top left match is found.
With image search it is still so, that it cannot be foreseen, which of the possible matches is returned as the result.
In doubt you have to use the functions, that return all matches in a region and then filter the result to your needs.

==================   ==================   =====================
**text-function**    **repeats-search**   **throws-FindFailed**
  findText             no                   yes
  waitText             yes                  yes
  hasText              no                   no
  existsText           yes                  no
==================   ==================   =====================

And there are new text functions, that only search once and do not throw findFailed:
 - ``findWord("a word")`` looks for a word, whose text can be a regular expression
 - ``findLine("some text")`` looks for and returns the line, that contains the text (might be a regular expression)

Revision of the findAll feature
-------------------------------

All findAll variants only search once and do not throw a FindFailed. If nothing was found, you have to check the result
for being empty (not found) or not. How to do that depends on the function variant.

As with the find functions there are also findAllText and findAllT for the text only findAll functions.

The function findAll returns a so called ``Match-Iterator`` (Java: Iterator<Match>), that reveals its content by the two
methods ``hasNext()`` (true if still some matches available or false) and ``next()` (return the next match in the row).
The function ``next`` is greedy in that it removes the returned match from the internally managed list of matches.
So if you wanted a list, you always had to insert a step, that collects the matches into a list first.

Now there are function variants, that ``return match lists`` instead (Java: List<Match>). For empty or not you have to check
the size/length of the list, that is 0 when empty (not found)::

    # these return an Iterator
    result = findAll("image")
    result = findAllText("some text") # or findAllT()
    result.hasNext() # True or False
    result.next() # the next match or null/None if no more match

    # these return lists
    result = findAllList("image") # equivalent to findAll()
    result = getAll("image") # shortcut for findAllList()
    result = findAllText("some text") # or findAllT()
    result = findWords("a word") # like findWord() but returns all matches in the region
    result = findLines("some text") # like findLine() but returns all matches in the region

For details you have to look into the description of the functions itself.
