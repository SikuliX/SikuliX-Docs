Finder
======

.. py:class:: Finder

**Be aware:** Since 1.1.0 the behavior is changed compared to previous versions,
to be consistent with Region.find()/findAll().

A Finder object implements an iterator of matches and allows to search for a visual
object in an image file that you provide (e.g. a screenshot taken and saved in a
file before). After setting up the finder object and doing a find operation, you can
iterate through the found matches if any.

**Be aware**: an iterator can be stepped through only once - it is empty afterwards

Compared with the region based find/findAll operation, no exception FindFailed is
raised in case nothing is found at all. Use ``hasNext()`` to check.

**Note**: There is no chance, to get the number of matches in
advance. If you iterate through to count, afterwards your finder is empty. 
So you have to save your matches somehow while counting, if you need them later (one possible solution
see example below).

The workflow always is (except for findChanges):
 * setup a Finder
 * do a find or findAll operation
 * check with hasNext(), wether anything was found at all
 * get the available matches with next() if hasNext() says more are available
 * After a complete iteration, the finder object is empty. 

You can start a new find or findAll operation on the same Finder object at any time.

.. py:class:: Finder

	.. py:method:: Finder(path-to-imagefile)

		Create a new finder object.

		:param path-to-imagefile: filename to a source image to search within
	
	.. py:method:: find(path-to-imagefile, [similarity])

		Find a given image within a source image previously specified in the
		constructor of the finder object. If more than one match is possible, yet only one is returned 
		and which one is not predictible.
		
		:param path-to-imagefile: the target image to search for
		:param similarity: the minimum similarity a match should have. If omitted,
			the default is used.
	
	.. py:method:: findAll(path-to-imagefile, [similarity])

		Find all occurences of the given image within a source image previously specified in the
		constructor of the finder object.
		
		:param path-to-imagefile: the target image to search for
		:param similarity: the minimum similarity a match should have. If omitted,
			the default is used.

	.. py:method:: hasNext()

		Check whether there are more matches available that satisfy the minimum
		similarity requirement. A *False* returned after the first hasNext() signals, 
		that nothing was found at all.

		:return: *True* if more matches exist.

	.. py:method:: next()

		Get the next match. 

		:return: a :py:class:`Match` object or None, if empty or no more matches.

		The returned reference to a match object is no longer available in the finder
		object afterwards. So if it is needed later, it has to be saved to
		another variable.

	.. py:method:: findChanges(path-to-imagefile)

    Find rectangle areas in an image (the one the Finder was created with), that differ from another image.
    This feature is the image variant of ``onChange`` in the Region observe feature for one-time-use.

    :param path-to-imagefile: the target image to compare with (exactly same size in pixels)
    :return: a list of Region objects (empty, if no changes where detected).


Example 1: findAll using a Finder

.. sikulicode::
	
	# create a Finder with your saved screenshot
	f = Finder("stars.png")
	img= "star.png" # the image you are searching
	
	f.findAll(img) # find all matches
	
	while f.hasNext(): # loop as long there is a first and more matches
		print "found: ", f.next() # access the next match in the row
	
	print f.hasNext() # is False, because f is empty now

Example 2: we want to know how many matches in advance 
and want to save the matches for later use (based on the previous example).

.. sikulicode::
	
	# create a Finder with your saved screenshot
	f = Finder("stars.png")
	img= "star.png" # the image you are searching
	
	f.findAll(img) # find all matches
	matches = [] # an empty list to store the matches

	while f.hasNext(): # loop as long there is a first and more matches
		matches.append(f.next())	# access next match and add to matches

	print f.hasNext() # is False, because f is empty now

	# now we have our matches saved in the list matches
	print len(matches) # the number of matches

	# we want to use our matches
	for m in matches:
		print m
