.. _Region:

Region (rectangular pixel area on a screen)
===========================================

.. py:class:: Region

The Region is besides Images/Patterns (called Visuals) and Matches (where a Visual was found and how) 
the **basic element in the SikuliX concept**. 
So be sure, you have understood all aspects of a Region.

A Region is a **rectangular area on a** :py:class:`Screen` and is defined by 
	1.	its upper left corner (x, y) being the pixel with this offset relative to the
		upper left corner of the screen (usually (0, 0) ) and
	2.	its dimension (w, h) as its width and height in pixels. 

**x, y, w, h are integer numbers** counting a distance in pixels.

A Region **does not know anything about it's visual content** (windows, pictures, graphics,
text, ...). It only knows :ref:`the position on the screen and its dimension
<CreatingaRegionSettingandGettingAttributes>`.

A :py:class:`Match`, being the result of a :py:meth:`Region.find` operation, basically is a Region in all aspects, 
just having a few additional attributes specific for a find result.

New Regions can be created in various ways:

* specify their position and dimension

* extend a given Region in all directions (expand or shrink)

* based on adjacent rectangles up to the bounds of the screen horizontally or vertically. 

* based on their corners

* as subregions being rows, columns or cells of a regular grid

* combine different Regions or use their intersection

You can use :py:meth:`Region.find`, to **search a given Visual being a rectangular pixel pattern** 
(given as an Image (filename or :py:class:`Image`) or a :py:class:`Pattern` object) within this Region.
If this Visual is found in the Region, the resulting :py:class:`Match` object
has a similarity score between >0 and 1. The lower the similarity score, the higher the chance,
that you got a false positive (found something else). To make your scripts robust against false positives,
you should take care, to get similarity scores >0.85 or even >0.9. 

If the Visual is given for the search as an Image , Sikuli uses a
**minimum similarity of 0.7**, which only returns Matches with a score >0.7.
This default value can be changed in :py:attr:`Settings.MinSimilarity`. 
A Pattern is searched with the optionally given minimum similarity using :py:meth:`Pattern.similar`.

:ref:`Find operations <FindinginsideaRegionandWaitingforaVisualEvent>` return a
:py:class:`Match` object, which has all attributes and methods of a Region
and can be used in the same way as a Region (e.g. find something or click another
target inside). A :py:class:`Match` has the size in pixels of the Visual used
for searching, the position where it was found, the similarity
score and the elapsed time.

Look here for more detailed information on :ref:`How SikuliX finds images on the screen <BasicsFind>`.

**Be aware:** every mouse or keyboard action, that specifies a Visual to search for, 
will internally do the respective find operation first, to evaluate the action target.

A Region **remembers the match of the last successful find operation**, 
all matches of the last successful :py:meth:`Region.findAll` and the elapsed time. 
With :py:meth:`Region.getLastMatch`, :py:meth:`Region.getLastMatches` 
and :py:meth:`Region.getLastTime` you can get these objects/value.

You can **wait for a Pattern** to appear
using :py:meth:`Region.wait` or wait for it to vanish using :py:meth:`Region.waitVanish`

**Every not successful find operation** (even those done internally with a click() ...) will raise
a :ref:`FindFailed exception <ExceptionFindFailed>`, that has to be handled in your script.
If you do not do that, your script will simply stop here with an error.

**If you do not want to handle these FindFailed exceptions**,
you might search for a Pattern using :py:meth:`exists <Region.exists>`, 
which just returns nothing (None/null) in case of not found.
So you simply check the return value for being a Match.

For other options to handle FindFailed situations see :ref:`FindFailed exception <ExceptionFindFailed>`.

During a find operation internally the search is repeated with a scan rate (standard 3 per second)
**until success or an optionally given timeout** (standard 3 seconds)
is reached, which then results in a :ref:`FindFailed exception <ExceptionFindFailed>`.

Sikuli supports **visual event driven programming**: You can tell a Region
:ref:`to observe that something appears, vanishes or changes <ObservingVisualEventsinaRegion>`. 
It is possible to wait for the completion of an
observation or let it run in the background, while your script 
continues running.
When one of the visual events happens, a handler in your script is called. Each
Region can only have one observer, but each observer can observe multiple visual events in that Region.
You might also check the status of a background observation later in your workflow, to handle events inline. 
Generally it is your responsibility to stop observations, but at termination of your script or Java program all observations are terminated automatically.

**NOTE:** For **hints and tips how to get robust and fast acting workflows** 
look into the :ref:`Best Practices <BestPractices>`.

.. _CreatingaRegionSettingandGettingAttributes:

Create a Region, Set and Get Attributes
-------------------------------------------------

**NOTES**

**In any case a newly created Region will be restricted to the boundaries of the
screen containing the largest part of the new Region**.

It displays an **error, if no part of the newly created Region is  
contained by any of the available screens**. Subsequent usages of such a Region  
might result in errors, exceptions or even crashes, if features are used, that 
access the screen. 

Use :py:meth:`Region.isValid` to check, wether a Region is contained by a screen.

**Create a new Region based on top left corner and size**

.. py:class:: Region

	.. py:method:: Region(x,y,w,h)
		Region(region)
		Region(Rectangle)

		Create a region object

		:param x: x position of a rectangle.
		:param y: y position of a rectangle.
		:param w: height of a rectangle.
		:param h: width of a rectangle.
		:param region: an existing region object.
		:param rectangle: an existing object of Java class Rectangle
		:return: a new region object.

		For **other ways to create new Regions** see: :ref:`Extend Regions ... <ExtendingaRegion>`.
		
		*NOTE:* The position and dimension attributes are named x, y 
		representing the top left corner and w, h being width and height.
		You might access/change these values directly or use the available getter/setter methods.
		::
		
			topLeft = Location(reg.x, reg.y) # equivalent to
			topLeft = reg.getTopLeft()
			
			theWidth = reg.w # equivalent to
			theWidth = reg.getW()
			
			reg.w = theWidth # equivalent to
			reg.setW(theWidth) 
	
		**Note:** Additionally you might use :py:meth:`selectRegion() <Screen.selectRegion>` 
		to interactively create a new region at runtime.

		**NOTE:** Using `Region(someOtherRegion)` just duplicates this region 
		(creates a new object). This can be useful, when
		you **need the same Region with different attributes**, such as another
		:ref:`observation loop <ObservingVisualEventsinaRegion>` 
		or another setting for :py:meth:`Region.setThrowException` to control
		whether throwing an exception or not when find ops fail. 

**Change a Regions position and/or size**

.. py:class:: Region

	.. py:method:: setX(number)
		setY(number)
		setW(number)
		setH(number)  

		Set the respective attribute of the region to the new value. This
		effectively moves the region around and/or changes its dimension.

		:param number: the new value

	.. py:method:: moveTo(location)
		
		Set the position of this region regarding it's top left corner 
		to the given location (the x and y values are modified).
		
		:param location: location object becomes the new top left corner
		:return: the modified region object
		
		::
		
			reg.moveTo(anotherLocation) # equivalent to
			reg.setX(anotherLocation.x); reg.setY(anotherLocation.y)

	.. py:method:: setROI(x,y,w,h)
		setROI(rectangle)
		setRect(x,y,w,h)
		setRect(rectangle)

		All these methods are doing exactly the same: 
			setting position and dimension to new values. 
			The motivation for two names is to make scripts more readable:
			``setROI()`` is intended to restrict the search to a smaller area to speed up
			processing searches (region of interest), whereas ``setRect()`` should be
			used to change a region (move and/or shrink or enlarge). 

		:param all x, y, w, h: the attributes of a rectangle
		:param rectangle: a rectangle object
		:return: None
		
	.. py:method:: morphTo(region)
		
		Set the position and dimension of this region to the corresponding values 
		of the region given as parameter. (see: :py:meth:`setRect() <Region.setRect>`)

		:param region: a region object		
		:return: the modified region object

		::

			reg.morphTo(anotherRegion) # equivalent to
			r = anotherRegion; reg.setX(r.x); reg.setY(r.y); reg.setW(r.w); reg.setH(r.h)

**Access a Region's attributes and settings**

.. py:class:: Region

	.. py:method:: getX()
		getY()
		getW()
		getH()  

		Get the respective attribute of the region.

		:return: integer value

	.. py:method:: getCenter()

		Get the center of the region.

		:return: an object of :py:class:`Location`
		
	.. py:method:: getTopLeft()
		getTopRight()
		getBottomLeft()
		getBottomRight()
		
		Get the location of the region's respective corner
		
		:return: Location object

	.. py:method:: getScreen()

		Returns the screen object that contains this region. 
      
		:return: a new :py:class:`Screen` object
		  
		See	:ref:`Multi Monitor Environments <MultiMonitorEnvironments>`.

	.. py:method:: getLastMatch()
		getLastMatches()

		To access the Matches returned by the last find op in this Region.

		:return: a :py:class:`Match` object or a list of Match objects

		All basic find operations (explicit like :py:meth:`Region.find()` or
		implicit like :py:meth:`Region.click()`) store the match in ``lastMatch`` 
		and multi-find ops (like :py:meth:`Region.findAll()`) 
		all found matches into ``lastMatches``  
		of the Region that was searched.

		:ref:`How to go through the Matches returned by getLastMatches() <IteratingMatches>`.
		
		**TIPP:** The ``LastMatch`` can be used to avoid a second search for 
		the same Visual in sequences like::
			
			wait(someVisual)
			click(someVisual)
			# or
			if exists(someOtherVisual):
				click(someOtherVisual)
				
		To avoid the second search with the ``click()`` you can use::
			
			wait(someVisual)
			click(getLastMatch())
			# or
			if exists(someOtherVisual):
				click(getLastMatch())
				
		There are convenience shortcuts for this::
			
			wait(someVisual)
			click()
			# or
			if exists(someOtherVisual):
				click()
		
		See :py:meth:`Region.click` for the usage of these convenience shortcuts.
		A ``someRegion.click()`` will either click the center of the given Region 
		or the ``lastMatch``, if any is available.		

	.. py:method:: getTime()

		:return: the elapsed time in number of milli-seconds of the last find op in this Region	
		
**Attributes influencing the behavior of features of a Region**

.. py:class:: Region

	**NOTE** For settings influencing the handling of Visual-not-found situations in 
	this Region look here: :ref:`FindFailed Exceptions <ExceptionFindFailed>`.
	
	.. py:method:: isRegionValid()
		
		:return: False, if the Region is not contained by a Screen and hence cannot 
			be used with faetures, that need a Screen (find, capture, ...), otherwise True. 

	.. py:method:: setAutoWaitTimeout(seconds)

		Set the maximum waiting time for all subsequent find operations in that Region.
		
		:param seconds: a number, which can have a fraction. The internal
			granularity is milli-seconds.

		All subsequent find ops will be run with the given timeout instead of the
		current value of :py:attr:`Settings.AutoWaitTimeout`, to which the region
		is initialized at time of creation (default 3 seconds).

	.. py:method:: getAutoWaitTimeout()

		Get the current value of the maximum waiting time for find ops in this region.
		
		:return: timeout in seconds

	.. py:method:: setWaitScanRate(rate)
	
		Set this Region's value: A find op should repeat the search 
		for the given Visual ``rate`` times per second
		until found or the maximum waiting time is reached.
		At time of Region creation the value is initialized 
		from :py:attr:`Settings.WaitScanRate`, which has a default of 3.
		
		:param rate: a value > 0. values < 1 will lead to scans every x seconds
			and hence longer pauses between the searches (reduces cpu load).
		
		**TIPP** Since on average the shortes search times are some milli seconds, 
		``rate`` > 100 will lead to a continous search under all circumstances.
	
	.. py:method:: getWaitScanRate()
	
		Get the current value of this Region's ``WaitScanRate``.
		
		:return: the rate number
		
.. _RegionGetParts

Get evenly sized parts of a Region (as rows, columns and cells based on a raster)
---------------------------------------------------------------------------------

In many cases, one has parts of a GUI, that are to some extent evenly structured, 
having some virtual raster (rows, columns and/or cells), that one wants to use 
for restricting searches or walk through this parts for other reasons.

Typical examples are tables like in an Excel sheet, boxes in some GUI or on a webpage
or dropdown lists and menues.

A given Region can be set to have some evenly sized raster, so that one can access
these subregions and create new Regions.

**Convenience functions, to get a subregion from a specified raster in one step**

.. py:class:: Region

	.. py:method:: get(somePart)
    	
    	Select a part of the given Region based on the given part specifier.
    	
    	:param somePart: a constant as Region.CONSTANT or an integer between 200 and 999 (see below)
    	
    	:return: a new Region created from the selected part
    	
    	**Usage based on the javadocs**::
    	
		Constants for the top parts of a region (Usage: Region.CONSTANT)
		shown in brackets: possible shortcuts for the part constant
		NORTH (NH, TH) - upper half 
		NORTH_WEST (NW, TL) - left third in upper third 
		NORTH_MID (NM, TM) - middle third in upper third 
		NORTH_EAST (NE, TR) - right third in upper third 
		... similar for the other directions: 
		right side: EAST (Ex, Rx)
		bottom part: SOUTH (Sx, Bx) 
		left side: WEST (Wx, Lx)
		
		specials for quartered:
		TT top left quarter
		RR top right quarter
		BB bottom right quarter
		LL bottom left quarter
		
		specials for the center parts:
		MID_VERTICAL (MV, CV) half of width vertically centered 
		MID_HORIZONTAL (MH, CH) half of height horizontally centered 
		MID_BIG (M2, C2) half of width / half of height centered 
		MID_THIRD (MM, CC) third of width / third of height centered 
		
		Based on the scheme behind these constants there is another possible usage:
		specify part as e 3 digit integer where the digits xyz have the following meaning
		1st x: use a raster of x rows and x columns
		2nd y: the row number of the wanted cell
		3rd z: the column number of the wanted cell
		y and z are counting from 0
		valid numbers: 200 up to 999 (< 200 are invalid and return the region itself) 
		example: get(522) will use a raster of 5 rows and 5 columns and return the cell in the middle
		special cases:
		if either y or z are == or > x: returns the respective row or column
		example: get(525) will use a raster of 5 rows and 5 columns and return the row in the middle

	Internally this is based on :py:meth:`Region.setRaster` and :py:meth:`Region.getCell`.
	
	If you need only one row in one column with x rows or 
	only one column in one row with x columns 
	you can use :py:meth:`Region.getRow` or :py:meth:`Region.getCol`
	
	.. py:method:: getRow(whichRow, numberRows)
	
		:param numberRows: in how many evenly sized rows should the region be devided
		:param whichRow: the row to select counting from 0, negative counts backwards from the end
	    	:return: a new Region created from the selected row

	.. py:method:: getCol(whichColumn, numberColumns)
	
		:param numberColumns: in how many evenly sized columns should the region be devided
		:param whichColumn: the column to select counting from 0, negative counts backwards from the end
    		:return: a new Region created from the selected column

**The basic functions for any raster setup**

.. py:class:: Region

	.. py:method:: setRows(numberRows)
		setCols(numberColumns)
		
		Define a rows or columns only raster, by dividing the Region's respective 
		dimension into even parts. The corresponding Regions will only be created,
		when the respective access methods are used later.
		
		:param numberRows: the number of rows the Region should be devided in 
		:param numberColumns: the number of columns the Region should be devided in 	    
		:return: the first element as new Region if successful or the region itself otherwise
	
	.. py:method:: setRaster(numberRows, numberColumns)
	
		Define a raster, by deviding the Region's height in ``numberRows`` even sized rows and
		it's width into ``numberColumns`` even sized columns.

		:param numberRows: the number of rows the Region should be devided in 
		:param numberColumns: the number of columns the Region should be devided in 	    				
		:return: the top left cell (``getCell(0, 0)``) if success, the Region itself if not
	
	.. py:method:: getRow(whichRow)
		getCol(whichColumn)
	 
		Get the Region of the ``whichRow`` row or ``whichColumn`` column 
		in the Region's valid raster counting from 0. 
		Negative values will count backwards from the end.
		Invalid indexes will return the last or first element respectively.
	
		:param whichRow: the number of the row to create a new Region from
		:param whichColumn: the number of the column to create a new Region from
		:return: a new Region representing the selected element or the Region if no raster

	.. py:method:: getCell(whichRow, whichColumn)
	
		Get the cell with the coordinates (``whichRow``, ``whichColumn``) 
		in the Region's valid raster counting from 0.
		Negative values will count backwards from the end.
		Invalid indexes will return the last or first element respectively.
		If the current raster only has rows or columns, the element of the 
		corresponding index will be returned.
		
		:param whichRow: the number of the row 
		:param whichColumn: the number of the column 
		:return: a new Region representing the selected element or the Region itself if no raster

**getting information about the current raster**

.. py:class:: Region
	
	.. py:method:: isRasterValid()
	
		Can be used to check, wether the Region currently has a valid raster
		
		:return: True if it has a valid raster (either getCols or getRows or both would return > 0) 
		
	.. py:method:: getRows()
		getCols()
					
		:return: the current raster setting (0 means not set) as number of rows/columns
	
	.. py:method:: getRowH()
		getColW()
					
		:return: the current raster setting (0 means not set) as height of one row or width of one column.
		
.. _ExtendingaRegion:

Extend Regions and create new Regions based on existing Regions
---------------------------------------------------------------------

**NOTES:**

Except otherwise noted

* these methods **return new Region objects**, whose location and size are based on the specified region.

* the given **base Region remains unchanged**.

**In any case the new Region will be restricted to the boundaries of the
screen containing the largest part of the new Region**.

It displays an **error, if no part of the new Region is  
contained by any of the available screens**. Subsequent usages of such a Region object 
might result in errors, exceptions or even crashes, if features are used, that 
acces the screen. 

Use :py:meth:`Region.isValid` to check, wether a Region is contained by a screen.

.. py:class:: Region

	.. py:method:: offset(location)
					offset(x, y)
	
		Creates a new Region object, whose upper left corner is relocated 
		adding the given x and y values to the respective values of the given Region.
		Width and height are the same. 
		
		:param location: a :py:class:`Location` object providing the relocating x and y values
		:param x: a number being the offset horizontally (< 0 to the left, > 0 to the right)
		:param y: a number being the offset vertically (< 0 to the top, > 0 to the bottom)
		:return: the new :py:class:`Region` object 
		
		::
		
			new_reg = reg.offset(Location(xoff, yoff)) # same as
			new_reg = Region(reg.x + xoff, reg.y + yoff, reg.w, reg.h)
			
	.. py:method:: inside()
	
		Returns the same object. Retained for upward compatibility.
		``region.inside().find()`` is totally equivalent to ``region.find()``.
		
		:return: Region itself

**NOTE:** Besides the individual methods like ``nearby, left, right, above, below`` there is one 
new method ``grow`` with some more options and different signatures. Where documented together,
they are fully equivalent. The reason behind is some better compatibility to the usage of Java Rectangle.
		
.. py:class:: Region

	.. py:method:: grow([range]) 
		nearby([range])

		The new region is defined by extending (>0) or shrinking (<0) the
		current region's dimensions in all directions by ``range`` number of pixels. 
		The center of the new region remains the same.
		
		The default is taken from :py:attr:`Settings.DefaultPadding` (standard value 50)

		:param range: an integer indicating the number of pixels or the current default if omitted.
		:return: a new :py:class:`Region` object
		
	.. py:method:: above([range])
		below([range])
		left([range])
		right([range])

		Returns a new :py:class:`Region` that is defined with respect to the given region:
			
			* above: new bottom edge next pixel row above given region's top edge
			* below: new top edge next pixel row below given region's bottom edge
			* left: new right edge next pixel clomn left of given region's left edge
			* right: new left edge next pixel column right of given region's right edge

		It does not include the current region. 
		If *range* is omitted, it reaches to the corresponding edge of the screen. 

		:param range: a positive integer defining the new demension aspect (width or height)
		:return: a new :py:class:`Region` object
		
	.. py:method:: grow(width, height)
	
.. _FindinginsideaRegionandWaitingforaVisualEvent:

Finding inside a Region and Waiting for a Visual Event
------------------------------------------------------

Besides :ref:`acting on visual objects <ActingonaRegion>`, finding them is one of the core
functions of Sikuli. 

**PS**: means, that either a :py:class:`Pattern` or a string (path to an image
file or just plain text) can be used as parameter. A find operation is
successful, if the given image is found with the given minimum similarity or
the given text is found exactly. Similarity is a value between 0 and 1 to
specify how likely the given image looks like the target. By default, the
similarity is 0.7 if an image rather than a pattern object with a specific
similarity is given to :py:meth:`Region.find()`.

Normally all these region methods are used as ``reg.find(PS)``, where reg is a
region object. If written as ``find(PS)`` it acts on the default screen, which is
an implicit region in this case (see: :ref:`SCREEN as Default Region <DefaultScreen>`). 
But in most cases it is a good idea to use
``region.find()`` to restrict the search to a smaller region in order to speed up
processing. 

If a find operation is successful, the returned match is additionally stored
internally with the region that was used for the search. So instead of using a
variable to store the match ( ``m = reg.find()`` ), you can use ``reg.getLastMatch()`` to access
it afterwards. Unsuccessful find operations will leave these values unchanged.

.. _PatternNotFound:

By default, if the **visual object (image or text) cannot be found**, Sikuli
will stop the script by raising an :ref:`Exception FindFailed <ExceptionFindFailed>`. 
This follows the standards of the Python language, so that you could handle such exceptions using
``try: ... except: ...``. 

If you are not used to programming using the Python language or because of other reasons, you might just want to bypass the
exception handling, which means just ignoring it (None is returned in that case). 
Or you might interactively react on a FindFailed situation (e.g. optionally repeat the find).
Read more about concepts and options at: :ref:`Exception FindFailed <ExceptionFindFailed>`.

If you have **multiple monitors**, please read 
:ref:`Multi Monitor Environments <MultiMonitorEnvironments>`.

**Note on IDE**: Capturing is a tool in the IDE, to quickly set up images to search
for. These images are named automatically by the IDE and stored together with
the script, at the time it is saved (we call the location in the file system
bundle-path). Behind the curtain, the images itself are specified simply by
using a string containing the file name (path to an image file). 

.. py:class:: Region

	.. py:method:: find(PS)

		:param PS: a :py:class:`Pattern` object or a string (path to an image file or just plain text)
		:return: a :py:class:`Match` object that contains the best match or fails if :ref:`not found <PatternNotFound>`

		Find a particular GUI element, which is seen as the given image or
		just plain text. The given file name of an image specifies the element's
		appearance. It searches within the region and returns the best match,
		which shows a similarity greater than the minimum similarity given by
		the pattern. If no similarity was set for the pattern by
		:py:meth:`Pattern.similar` before, a default minimum similarity of 0.7
		is set automatically. 
		
		If autoWaitTimeout is set to a non-zero value, find() just acts as a wait().

		**Side Effect** *lastMatch*: the best match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

	.. py:method:: findAll(PS)

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text)
		:return: one or more :py:class:`Match` objects as an iterator object or fails if :ref:`not found <PatternNotFound>`
		
		How to iterate through is :ref:`documented here <IteratingMatches>`. 

		Repeatedly find ALL instances of a pattern, until no match can be
		found anymore, that meets the requirements for a single
		:py:meth:`Region.find()` with the specified pattern.

		By default, the returned matches are sorted by the similiarty.
		If you need them ordered by their positions, say the Y coordinates,
		you have to use Python's `sorted <http://wiki.python.org/moin/HowTo/Sorting/>`_ function. Here is a example of sorting the matches from top to bottom.

		.. sikulicode::

			def by_y(match):
			   return match.y

			icons = findAll("png_icon.png")
			# sort the icons by their y coordinates and put them into a new variable sorted_icons
			sorted_icons = sorted(icons, key=by_y) 
			# another shorter version is using lambda.
			sorted_icons = sorted(icons, key=lambda m:m.y)
			for icon in sorted_icons:
			   pass # do whatever you want to do with the sorted icons


                
		**Side Effect** *lastMatches*: a reference to the returned iterator object containing the 
		found matches is stored with the region that was searched. It can be
		accessed using getLastMatches() afterwards. How to iterate through an
		iterator of matches is :ref:`documented here <IteratingMatches>`.

	.. py:method:: wait([PS],[seconds])

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text)
		:param seconds: a number, which can have a fraction, as maximum waiting
			time in seconds. The internal granularity is milliseconds. If not
			specified, the auto wait timeout value set by
			:py:meth:`Region.setAutoWaitTimeout` is used. Use the constant
			*FOREVER* to wait for an infinite time. 
		:return: a :py:class:`Match` object that contains the best match or fails if :ref:`not found <PatternNotFound>`

		If *PS* is not specified, the script just pauses for the specified
		amount of time. It is still possible to use ``sleep(seconds)`` instead,
		but this is deprecated.

		If *PS* is specified, it keeps searching the given pattern in the
		region until the image appears ( would have been found with
		:py:meth:`Region.find`) or the specified amount of time has elapsed. At
		least one find operation is performed, even if 0 seconds is specified.) 

		**Side Effect** *lastMatch*: the best match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

		Note: You may adjust the scan rate (how often a search during the wait
		takes place) by setting :py:attr:`Settings.WaitScanRate` appropriately. 

	.. py:method:: waitVanish(PS, [seconds])

		Wait until the give pattern *PS* in the region vanishes.

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text)
		:param seconds: a number, which can have a fraction, as maximum waiting
			time in seconds. The internal granularity is milliseconds. If not
			specified, the auto wait timeout value set by
			:py:meth:`Region.setAutoWaitTimeout` is used. Use the constant
			*FOREVER* to wait for an infinite time.
		:return: *True* if the pattern vanishes within the specified waiting
			time, or *False* if the pattern stays visible after the waiting time
			has elapsed.

		This method keeps searching the given pattern in the region until the
		image vanishes (can not be found with :py:meth:`Region.find` any
		longer) or the specified amount of time has elapsed. At least one find
		operation is performed, even if 0 seconds is specified. 

		**Note**: You may adjust the scan rate (how often a search during the wait
		takes place) by setting :py:attr:`Settings.WaitScanRate` appropriately. 

	.. py:method:: exists(PS, [seconds])

		Check whether the give pattern is visible on the screen.

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text)
		:param seconds: a number, which can have a fraction, as maximum waiting
			time in seconds. The internal granularity is milliseconds. If not
			specified, the auto wait timeout value set by
			:py:meth:`Region.setAutoWaitTimeout` is used. Use the constant
			*FOREVER* to wait for an infinite time.
		:return: a :py:class:`Match` object that contains the best match. None
			is returned, if nothing is found within the specified waiting time

		Does exactly the same as :py:meth:`Region.wait()`, but no exception is
		raised in case of FindFailed. So it can be used to symplify scripting
		in case that you only want to know wether something is there or not to
		decide how to proceed in your workflow. So it is typically used with an
		if statement.  At least one find operation is performed, even if 0
		seconds is specified. So specifying 0 seconds saves some time, in case
		there is no need to wait, since its your intention to get the
		information "not found" directly. 

		**Side Effect** *lastMatch*: the best match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

		**Note**: You may adjust the scan rate (how often a search during the wait
		takes place) by setting :py:attr:`Settings.WaitScanRate` appropriately. 

.. _ObservingVisualEventsinaRegion:

Observing Visual Events in a Region
-----------------------------------

**This feature is completetely revised in version 1.1.0**

You can tell a region to observe that something appears or vanishes,
or something changes in that region. Using the methods 
:py:meth:`Region.onAppear`, :py:meth:`Region.onVanish` and :py:meth:`Region.onChange`, 
you register an event to be observed, while the observation is running for that Region.
The observation in a Region is started using :py:meth:`Region.observe` 
and stopped again using :py:meth:`Region.stopObserveer`.

Each Region can have exactly one observer.
For each observer, you can register as many events as
needed. So you can think of it as grouping some ``wait()`` and ``waitVanish()``
together and have them processed simultanouesly, while you are waiting for one
of these events to happen.

It is possible to let the script wait for the completion of an observation or
let the observation run in background (meaning in parallel), while your script is continuing. 
With a timing parameter you can tell :py:meth:`Region.observe` 
to stop observation after the given time.

When one of the visual events happens, an event handler (callback function) provided by you is
called, handing over a :py:class:`SikuliEvent` object as a parameter, that contains all relevant information about 
the event and that has features to act on the events or change the behavior of the observation. 
During the processing in your handler, the observation is paused until your handler has ended. 
Information between the main script and your handlers 
can be given forward and backward using global variables or other appropriate measures.

Another option to handle events, that are observed in the background, is to check the status of the observation 
inline in your workflow. Each registered event has a unique name, that later can be used, to check, wether it already happened or not. Furthermore you can inactivate registered events, so that they are ignored until activated again. 

It's your responsibility to stop the observation. This can either be done by
calling :py:meth:`Region.stopObserver` (in the main workflow or in the handler)
or by starting the observation with a timing parameter. All running observations are stopped
automatically, when the script or Java program (in fact the JVM) terminates.

Since you can have as many region objects as needed and each region can have
one observation active and running, theoretically it is possible to have as
many visual events being observed at the same time as needed. But in reality,
the number of observations is limited by the system resources available to
Sikuli at that time.

Be aware, that every observation is a number of different find operations that
are processed repeatedly. So to speed up processing and keep your script
acting, you should define a region for observation as small as possible.
You may adjust the scan rate (how often a search during the observation takes
place) by setting :py:attr:`Settings.ObserveScanRate` appropriately. 

**PS**: means, that either a Pattern or a String (path to an image file or just
plain text) can be used as parameter.

.. _ObserveHandler:

**handler**: as a parameter in the following methods you have to specify the **name
of a function**, which will be called by the observer, in case the observed event
happens. The function name (and usually the function itself) has to be defined in your script before using the
appropriate functions to register an observe event. The existance of the function will be
checked before starting the script. 

So to get your script running, you have to
have at least the following statements in your script::

	def myHandler(event): # you can choose any valid function name
		# event: can be any variable name, it references the ObserveEvent object
		pass # add your statements here

	onAppear("path-to-an-image-file", myHandler) # or any other onXYZ()
	observe(10) # observes for 10 seconds

**Note for Java** And this is how you setup a handler in your Java program::

	someRegion.onAppear("path-to-an-image-file", 
		new ObserveCallback() {
			@Override
			public void appeared(ObserveEvent event) {
				// here goes your handler code
			}
		}
	);
	
Here ObserveCallback is an abstract class defining the three possible callback funtions appeared, vanished and changed, that has to be overwritten as needed in your implementation of the ObserveCallback.
	
Read :py:class:`ObserveEvent` to know what is contained in the event object and what its features are.

.. py:class:: Region

	.. py:method:: onAppear(PS, handler)

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text.

		:param handler: the name of a handler function in the script

		With the given region you register an observer, that should wait for
		the pattern to be there or to appaear and is activated with the next
		call of ``observe()``. In the moment the internal find operation with the
		given pattern is successful during observation, your handler is called
		and the observation is paused until you return from your handler.
		
		With the first appearence, the observation for this event is terminated.
		If you want the observation for this event to be continued, you have to use
		:py:meth:`SikuliEvent.repeat` before leaving the handler.

	.. py:method:: onVanish(PS, handler)

		:param PS: a :py:class:`Pattern` object or a string (path to an image
			file or just plain text.

		:param handler: the name of a handler function in the script

		With the given region you register an observer, that should wait for
		the pattern to be not there or to vanish and is activated with the next
		call of ``observe()``. In the moment the internal find operation on the
		given pattern fails during observation, your handler is called and the
		observation is paused until you return from your handler. 

		With the first vanishing, the observation for this event is terminated.
		If you want the observation for this event to be continued, you have to use
		:py:meth:`SikuliEvent.repeat` before leaving the handler.

	.. py:method:: onChange([minChangedSize], handler)

		:param minChangedSize: the minimum size in pixels of a change to trigger a change event.
			If omitted: 50 is used (see :py:attr:`Settings.ObserveMinChangedPixels`).
		:param handler: the name of a handler function in the script
		
		With the given region you register an observer, that should wait for
		the visual content of the given region to change and is activated with
		the next call of ``observe()``. In the moment the visual content changes
		during observation, your handler is called and the observation is
		paused until you return from your handler.

		Here is a example that highlights all changes in an observed region.
		::

			def changed(event):
				print "something changed in ", event.region
				for ch in event.getChanges():
					ch.highlight() # highlight all changes
				wait(1)
				for ch in event.getChanges():
					ch.highlight() # turn off the highlights
		
			r = selectRegion("select a region to observe")
			# any change in r larger than 50 pixels would trigger the changed function
			r.onChange(50, changed) 
			r.observe(background=True)

			wait(30) # another way to observe for 30 seconds
			r.stopObserver()


	.. py:method:: observe([seconds], [background = False | True])

		Begin observation within the region.

		:param seconds: a number, which can have a fraction, as maximum
			observation time in seconds. Omit it or use the constant FOREVER to
			tell the observation to run for an infinite time (or until stopped
			by ``stopObserve()``). 
		
		:param background: a flag indicating whether observation is run in the
			background. when set to *True*, the observation will be run in a
			subthread and processing of your script is continued immediately.
			Otherwise the script is paused until the completion of the
			observation.

		For each region object, only one observation can be running at a given time.

		**Note**: You may adjust the scan rate (how often a search during the
		observation takes place) by setting :py:attr:`Settings.ObserveScanRate`
		appropriately. 
		
	.. py:method:: stopObserver()

		Stop observation for this region.

		This must be called on a valid region object. The source region of an
		observed visual event is available as one of the attributes of the *event*
		parameter that is passed to the handler function when the function is
		invoked. 
		
		For example, to stop observation within a handler function, simply
		call ``event.stopObserver()`` inside the handler function.::
		
			def myHandler(event): 
				event.stopObserver() # stops the observation
						
			onAppear("path-to-an-image-file", myHandler) 
			observe(FOREVER) # observes until stopped in handler


.. py:class:: SikuliEvent

   When processing an :ref:`observation in a region <ObservingVisualEventsinaRegion>`, 
   a :ref:`handler function is called <ObserveHandler>`, when one of your 
   registered events :py:meth:`Region.onAppear`, :py:meth:`Region.onVanish` or :py:meth:`Region.onChange` happen.
   
   The one parameter, you have access to in your handler function is an instance
   of *Class SikuliEvent*. You have access to the following attributes of the event, 
   that might help to identify the cause of the event and act on the resulting matches.
   
   *Usage:* ``event.attribute`` 
      where ``event`` is the parameter from the definition of your handler function: ``def myHandler(event):``

   .. py:attribute:: type

   The :py:attr:`type` of this event can be 
   :py:const:`SikuliEvent.Type.APPEAR`, :py:const:`SikuliEvent.Type.VANISH`,
   or :py:const:`SikuliEvent.Type.CHANGE`.

   .. py:attribute:: pattern

   The source pattern that triggered this event. This is only valid
   in APPEAR and VANISH events.

   .. py:attribute:: region

   The source region of this event.

   .. py:attribute:: match

   In an APPEAR event, *match* saves the top :py:class:`Match` object
   that appeared in the observed region.

   In a VANISH event, *match* saves the *last* :py:class:`Match` object
   that was in the observed region but vanished.

   This attribute is not valid in a CHANGE event.

   .. py:attribute:: changes

   This attribute is valid only in a CHANGE event.

   This *changes* attribute is a list of :py:class:`Match` objects that
   changed and their sizes are at least :py:meth:`minChangedSize <Region.onChange>` pixels.


.. _ActingonaRegion:

Acting on a Region
------------------

Besides :ref:`finding visual objects <FindinginsideaRegionandWaitingforaVisualEvent>` 
on the screen, acting on these elements is one of the
kernel operations of Sikuli. Mouse actions can be simulated as well as pressing keys
on a keyboard.

The place on the screen, that should be acted on (in the end just one specific
pixel, the click point), can be given either as a :py:class:`pattern <Pattern>` like with the find
operations or by directly referencing a pixel :py:class:`location <Location>` 
or as center of a :py:class:`region <Region>` object (:py:class:`match <Match>` or
:py:class:`screen <Screen>` also) or the target offset location connected with a :py:class:`match <Match>`. Since all
these choices can be used with all action methods as needed, they are abbreviated
and called like this:

**PSMRL:** which means, that either a :py:class:`Pattern` object or a string (path to an image file or just
plain text) or a :py:class:`Match` or a :py:class:`Region` or a :py:class:`Location` can be used as parameter, in
detail: 

*	**P**: *pattern*: a :py:class:`Pattern` object. An implicit find operation is
	processed first. If successful, the center of the resulting matches rectangle is
	the click point. If the pattern object has a target offset specified, this is
	used as click point instead. 

*	**S**: *string*: a path to an image file or just plain text. An implicit find
	operation with the default minimum similarity 0.7 is processed first. If
	successful, the center of the resulting match object is the click point. 

*	**M**: *match:* a :py:class:`match <Match>` object from a previous find operation. If the match has a target
	specified it is used as the click point, otherwise the center of the match's
	rectangle. 

*	**R**: *region:* a :py:class:`region <Region>` object whose center is used as click point. 

*	**L**: *location:* a :py:class:`location <Location>` object which by definition represents a point on the
	screen that is used as click point. 

It is possible to simulate pressing the so called :doc:`key modifiers <keys>` together with the mouse
operation or when simulating keyboard typing. The respective parameter is given by
one or more predefined constants. If more than one modifier is necessary, they are
combined by using "+" or "|".

Normally all these region methods are used as ``reg.click(PS)``, where reg is a
region object. If written as ``click(PS)`` the implicit find is done on the default
screen being the implicit region in this case (see: :ref:`SCREEN as Default Region <DefaultScreen>`). 
But using ``reg.click(PS)`` will
restrict the search to the region's rectangle and speed up processing, if region is
significantly smaller than the whole screen.

Generally all aspects of :ref:`find operations
<FindinginsideaRegionandWaitingforaVisualEvent>` and of :py:meth:`Region.find`
apply. 

If the find operation was successful, the match that was acted on, can be
recalled using :py:meth:`Region.getLastMatch`. 

As a default, if the **visual object (image or text) cannot be found**, Sikuli
will stop the script by raising an :ref:`Exception FindFailed <ExceptionFindFailed>` 
(details see: :ref:`not found <PatternNotFound>`).

**Note on IDE**: Capturing is a tool in the IDE, to quickly set up images to search for.
These images are named automatically by the IDE and stored together with the script,
at the time it is saved (we call the location in the file system bundle-path).
Behind the curtain the images itself are specified by using a string containing the
file name (path to an image file).

**Note**: If you need to implement more sophisticated mouse and keyboard actions look at
:ref:`Low Level Mouse and Keyboard Actions <LowLevelMouseAndKeyboardActions>`.

**Note**: In case of having more than one Monitor active, refer to :ref:`Multi Monitor
Environments <MultimonitorEnvironments>` for more details.

**Note on Mac**: it might be necessary, to use :py:func:`switchApp` before, to prepare the
application for accepting the action.

.. py:class:: Region

	.. py:method:: click(PSMRL, [modifiers])

		Perform a mouse click on the click point using the left button.
		
		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers

		:return: the number of performed clicks (actually 1). A 0 (integer null)
			means that because of some reason, no click could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.
		
		**Example:**
		
		.. sikulicode::
		 
			# Windows XP
			click("xpstart.png")
		
			# Windows Vista
			click("vistastart.png")
		
			# Windows 7
			click("w7start.png")
		
			# Mac
			click("apple.png")
		

	.. py:method:: doubleClick(PSMRL, [modifiers])

		Perform a mouse double-click on the click point using the left button.
		
		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers

		:return: the number of performed double-clicks (actually 1). A 0 (integer null)
			means that because of some reason, no click could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).
			
		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.


	.. py:method:: rightClick(PSMRL, [modifiers])

		Perform a mouse click on the click point using the right button.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers

		:return: the number of performed right cilicks (actually 1). A 0 (integer null)
			means that because of some reason, no click could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

	.. py:method:: highlight([seconds])

		Highlight the region for some period of time.

		:param seconds: a decimal number taken as duration in seconds

		The region is highlighted showing a red colored frame around it. If the
		parameter seconds  is given, the script is suspended for the specified time.
		If no time is given, the highlighting is started and the script continues.
		When later on the same highlight call without a parameter is made, the
		highlighting is stopped (behaves like a toggling switch). 

		Example::

			m = find(some_image)

			# the red frame will blink for about 7 - 8 seconds
			for i in range(5):
				m.highlight(1)
				wait(0.5)

			# a second red frame will blink as an overlay to the first one
			m.highlight()
			for i in range(5):
				m.highlight(1)
				wait(0.5)
			m.highlight()

			# the red frame will grow 5 times
			for i in range(5):
				m.highlight(1)
				m = m.nearby(20)

		**Note**: The red frame is just an overlay in front of all other screen content and
		stays in its place, independently from the behavior of this other content,
		which means it is not "connected" to the *content* of the defining region. 
		But it will be adjusted automatically, if you change position and/or dimension 
		of this region in your script, while it is highlighted.

	.. py:method:: hover(PSMRL)

		Move the mouse cursor to hover above a click point.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers

		:return: the number 1 if the mousepointer could be moved to the click point.
			A 0 (integer null) returned
			means that because of some reason, no move could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.


	.. py:method:: dragDrop(PSMRL, PSMRL, [modifiers])

		Perform a drag-and-drop operation from a starting click point to the target
		click point indicated by the two PSMRLs respectively.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers
		
		If one of the parameters is *PS*, the operation might fail due to :ref:`not Found <PatternNotFound>`.

		**Sideeffect**: when using *PS*, the match of the target can be accessed using
		:py:meth:`Region.getLastMatch` afterwards. If only the first parameter is
		given as *PS*, this match is returned by :py:meth:`Region.getLastMatch`.

		**When the operation does not perform as expected** (usually caused by timing
		problems due to delayed reactions of applications), you may adjust the
		internal timing parameters :py:attr:`Settings.DelayAfterDrag` and
		:py:attr:`Settings.DelayBeforeDrop` eventually combined with the internal
		timing parameter :py:attr:`Settings.MoveMouseDelay`.

		Another solution might be, to use a combination of :py:meth:`Region.drag`
		and :py:meth:`Region.dropAt` combined with your own ``wait()`` usages.  If the mouse
		movement from source to target is the problem, you might break up the move
		path into short steps using :py:meth:`Region.mouseMove`. 

		**Note**: If you need to implement more sophisticated mouse and keyboard actions
		look at :ref:`Low Level Mouse and Keyboard Actions <LowLevelMouseAndKeyboardActions>`. 


	.. py:method:: drag(PSMRL)

		Start a drag-and-drop operation by dragging at the given click point.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:return: the number 1 if the operation could be performed. A 0 (integer null) returned
			means that because of some reason, no move could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).
			
		The mousepointer is moved to the click point and the left mouse button is
		pressed and held, until another mouse action is performed (e.g. a
		:py:meth:`Region.dropAt()`	afterwards). This is nomally used to start a
		drag-and-drop operation.

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

	.. py:method:: dropAt(PSMRL, [delay])

		Complete a drag-and-drop operation by dropping a previously dragged item at
		the given target click point.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param delay: time to wait after in seconds as decimal value

		:return: the number 1 if the operation could be performed. A 0 (integer null) returned
			means that because of some reason, no move could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).

		The mousepointer is moved to the click point. After waiting for delay
		seconds the left mouse button is released. This is normally used to finalize
		a drag-and-drop operation. If it is necessary to visit one ore more click
		points after dragging and before dropping, you can use
		:py:meth:`Region.mouseMove` inbetween.

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

	.. py:method:: type([PSMRL], text, [modifiers])

		Type the text at the current focused input field or at a click point
		specified by *PSMRL*.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more modifier keys (:doc:`Class Key <keys>`)

		:returns: the number 1 if the operation could be
			performed, otherwise 0 (integer null), which means,
			that because of some reason, it was not possible or the click 
			could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`).
		
		This method simulates keyboard typing interpreting the characters of text
		based on the layout/keymap of the **standard US keyboard (QWERTY)**. 
		
		Special
		keys (ENTER, TAB, BACKSPACE, ...) can be incorporated into text using the
		constants defined in :doc:`Class Key <keys>` using the standard string concatenation `+`.
		
		If *PSMRL* is given, a click on the clickpoint is performed before typing, to
		gain the focus. (Mac: it might be necessary, to use :py:func:`switchApp`
		to give focus to a target application before, to accept typed/pasted characters.)

		If *PSMRL* is omitted, it performs the typing on the current focused visual
		component (normally an input field or an menu entry that can be selected by
		typing something). 

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

		**Note**: If you need to type international characters or you are using
		layouts/keymaps other than US-QWERTY, you should use :py:meth:`Region.paste`
		instead. Since type() is rather slow because it simulates each key press,
		for longer text it is preferrable to use :py:meth:`Region.paste`.
		
		**Best Practice**: As a general guideline, the best choice is to use ``paste()``
		for readable text and ``type()`` for action keys like TAB, ENTER, ESC, ....
		Use one ``type()`` for each key or key combination and be aware, that in some cases 
		a short ``wait()`` after a ``type()`` might be necessary 
		to give the target application some time to react and be prepared 
		for the next Sikuli action. 

	.. py:method:: paste([PSMRL], text)

		Paste the text at a click point.		

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param modifiers: one or more key modifiers

		:returns: the number 1 if the operation could be performed, otherwise 0
			(integer null), which means,
			that because of some reason, it was not possible or the click 
			could be performed (in case of *PS* may be :ref:`not Found <PatternNotFound>`). 

		Pastes *text* using the clipboard (OS-level shortcut (Ctrl-V or Cmd-V)). So
		afterwards your clipboard contains *text*. ``paste()`` is a temporary solution for
		typing international characters or typing on keyboard layouts other than
		US-QWERTY.

		If *PSMRL* is given, a click on the clickpoint is performed before typing, to
		gain the focus. (Mac: it might be necessary, to use :py:func:`switchApp`
		to give focus to a target application before, to accept typed/pasted characters.)

		If *PSMRL* is omitted, it performs the paste on the current focused component
		(normally an input field).

		**Side Effect** if *PS* was used, the match can be accessed using :py:meth:`Region.getLastMatch` afterwards.

		**Note**: Special keys (ENTER, TAB, BACKSPACE, ...) cannot be used with ``paste()``.
		If needed, you have to split your complete text into two or more ``paste()``
		and use ``type()`` for typing the special keys inbetween. 
		Characters like \\n	(enter/new line) and \\t (tab) should work as expected with ``paste()``, 
		but be aware of timing problems, when using e.g. intervening \\t to jump 
		to the next input field of a form.
		
.. _RegionExtractingText: 

Extracting Text from a Region
-----------------------------

.. py:class:: Region

	.. py:method:: text()

		Extract the text contained in the region using OCR.

		:return: the text as a string. Multiple lines of text are separated by intervening '\n'.
		
		**Note**: Since this feature is still in an **experimental state**, be aware, 
		that in some cases it might not work as expected. If you face any problems 
		look at the `Questions & Answers / FAQ's <https://answers.launchpad.net/sikuli>`_
		and the `Bugs <https://answers.launchpad.net/sikuli>`_.

.. _LowLevelMouseAndKeyboardActions:

Low-level Mouse and Keyboard Actions
------------------------------------

.. py:class:: Region

	.. py:method:: mouseDown(button)

		Press the mouse *button* down.

		:param button: one or a combination of the button constants ``Button.LEFT``,
			``Button.MIDDLE``, ``Button.RIGHT``. 

		:return: the number 1 if the operation is performed successfully, and zero if
			otherwise.

		The mouse button or buttons specified by *button* are pressed until another
		mouse action is performed.

	.. py:method:: mouseUp([button])

		Release the mouse button previously pressed.

		:param button: one or a combination of the button constants ``Button.LEFT``,
			``Button.MIDDLE``, ``Button.RIGHT``. 

		:return: the number 1 if the operation is performed successfully, and zero if
			otherwise.

		The button specified by *button* is released. If *button* is omitted, all
		currently pressed buttons are released.

	.. py:method:: mouseMove(PSRML)

		Move the mouse pointer to a location indicated by PSRML.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:return: the number 1 if the operation could be performed. If using *PS*
			(which invokes an implicity find operation), find fails and you have
			switched off FindFailed exception, a 0 (integer null) is returned.
			Otherwise, the script is stopped with a FindFailed exception.

		**Sideeffects**: when using *PS*, the match can be accessed using
		:py:meth:`Region.getLastMatch` afterwards

	.. py:method:: mouseMove(xoff, yoff)

		Move the mouse pointer from it's current position to the position 
		given by the offset values (<0 left, up  >0 right, down)
		
		:param xoff: horizontal offset
		:param yoff: vertical offset
		:return: 1 if possible, 0 otherwise

	.. py:method:: wheel(PSRML, WHEEL_DOWN | WHEEL_UP, steps)

		Move the mouse pointer to a location indicated by PSRML and turn the mouse
		wheel in the specified direction by the specified number of steps.

		:param PSMRL: a pattern, a string, a match, a region or a location that
			evaluates to a click point.

		:param WHEEL_DOWN|WHEEL_UP: one of the two constants denoting the wheeling
			direction.

		:param steps: an integer indicating the amoung of wheeling.

		**Sideeffects**: when using *PS*, the match can be accessed using
		:py:meth:`Region.getLastMatch` afterwards

	.. py:method:: keyDown(key | list-of-keys)
	
		Press and hold the specified key(s) until released by a later call to
		:py:meth:`Region.keyUp`.

		:param key|list-of-keys: one or more keys (use the consts of class Key). A
			list of keys is a concatenation of several key constants using "+".

		:return: the number 1 if the operation could be performed and 0 if
			otherwise.

	.. py:method:: keyUp([key | list-of-keys])

		Release given keys. If no key is given, all currently pressed keys are
		released.

		:param key|list-of-keys: one or more keys (use the consts of class Key). A
			list of keys is a concatenation of several key constants using "+".

		:return: the number 1 if the operation could be performed and 0 if
			otherwise.

.. _ExceptionFindFailed:

Exception FindFailed
--------------------

As a default, find operations (:ref:`explicit <FindinginsideaRegionandWaitingforaVisualEvent>` 
and :ref:`implicit <ActingonaRegion>`) when not successful 
raise an ``Exception FindFailed``, that will
stop the script immediately. This is of great help, when developing a script, to
step by step adjust timing and similarity. When the script runs perfectly, then an
exception FindFailed signals, that something is not as it should be.

To implement some checkpoints, where you want to asure your workflow, use
:py:meth:`Region.exists`, that reports a not found situation without raising
FindFailed (returns False instead).

To run all or only parts of your script without FindFailed exceptions to be raised,
use :py:meth:`Region.setThrowException` or :py:meth:`Region.setFindFailedResponse`
to switch it on and off as needed.

For more sophisticated concepts, you can implement your own exception handling using
the standard Python construct ``try: ... except: ...`` .

.. versionadded:: X1.0-rc2

These are the possibilities to handle "not found" situations:
	* generally abort a script, if not handled with ``try: ... except: ...``
		(the default setting or using :py:meth:`setThrowException(True) <Region.setThrowException>` 
		or :py:meth:`setFindFailedResponse(ABORT) <Region.setFindFailedResponse>`) 
	* generally ignore all "not found" situations 
		(using :py:meth:`setThrowException(False) <Region.setThrowException>` 
		or :py:meth:`setFindFailedResponse(SKIP) <Region.setFindFailedResponse>`), 
	* want to be prompted in such a case 
		(using :py:meth:`setFindFailedResponse(PROMPT) <Region.setFindFailedResponse>`)
	* advise Sikuli to wait forever (be careful with that!)
		(using :py:meth:`setFindFailedResponse(RETRY) <Region.setFindFailedResponse>`)

.. _FindFailedPrompt:

**Comment on using PROMPT**: 

This feature is helpful in following situations:

* you are developing something, that needs an application with it's windows to be in place, but this workflow you want to script later. If it comes to that point, you get the prompt, arrange the app and click *Retry*. Your workflow should continue.
* you have a workflow, where the user might do some corrective actions, if you get a FindFailed
* guess you find more ;-)

In case of a FindFailed, you get the following prompt:

.. image:: findfailed-prompt.png

Clicking *Retry* would again try to find the image. *Skip* would continue the script and *Abort* would end the script.

**Examples**: 4 solutions for a case, where you want to decide how to proceed in a
workflow based on the fact that a specific image can be found. (pass is the python
statement, that does nothing, but maintains indentation to form the blocks)::

	# --- nice and easy
	if exists("path-to-image"): # no exception, returns None when not found
		pass # it is there
	else:
		pass # we miss it

	# --- using exception handling
	# every not found in the try block will switch to the except block
	try:
		find("path-to-image")
		pass # it is there
	except FindFailed:
		pass # we miss it

	# --- using setFindFailedResponse
	setFindFailedResponse(SKIP) # no exception raised, not found returns None
	if find("path-to-image"):
		setFindFailedResponse(ABORT) # reset to default
		pass # it is there
	else:
		setFindFailedResponse(ABORT) # reset to default
		pass # we miss it

	# --- using setThrowException
	setThrowException(False) # no exception raised, not found returns None
	if find("path-to-image"):
		setThrowException(True) # reset to default
		pass # it is there
	else:
		setThrowException(True) # reset to default
		pass # we miss it

.. py:class:: Region

	**Reminder** If used without specifying a region, the default/primary screen
	(default region SCREEN) is used. 

	.. versionadded:: X1.0-rc2
	.. py:method:: setFindFailedResponse(ABORT | SKIP | PROMPT | RETRY)
	
		For the specified region set the option, how Sikuli should handle 
		"not found" situations. The option stays in effect until changed 
		by another ``setFindFailedResponse()``.
	
		:param ABORT: all subsequent find failed operations (explicit or
			implicit) will raise exception FindFailed (which is the default when a
			script is started). 

		:param SKIP: all subsequent find operations will not raise
			exception FindFailed. Instead, explicit find operations such as
			:py:meth:`Region.find` will return *None*. Implicit find operations 
			(action functions) such as :py:meth:`Region.click` will do
			nothing and return 0.
		
		:param PROMPT: all subsequent find operations will not raise
			exception FindFailed. Instead you will be prompted for the way 
			to handle the situation (see :ref:`using PROMPT <FindFailedPrompt>`). Only the current 
			find operation is affected by your response to the prompt. 
	
		:param RETRY: all subsequent find operations will not raise
			exception FindFailed. Instead, Sikuli will try to find the target 
			until it gets visible. This is equivalent to using ``wait( ... , FOREVER)``
			instead of ``find()`` or using setAutoWaitTimeout(FOREVER).
			
	.. py:method:: getFindFailedResponse()
	
		Get the current setting in this region.
	
		:return: ABORT or SKIP or PROMPT or RETRY
		
		Usage::
		
			val = getFindFailedResponse()
			if val == ABORT:
				print "not found will stop script with Exception FindFailed"
			elif val == SKIP:
				print "not found will be ignored"
			elif val == PROMPT:
				print "when not found you will be prompted"
			elif val == RETRY:
				print "we will always wait forever"
			else:
				print val, ": this is a bug :-("

	**Note**: It is recommended to use ``set/getFindFailedResponse()`` instead of 
	``set/getThrowException()`` since the latter ones might be deprecated in the future.
	
	.. py:method:: setThrowException(False | True)
	
		By using this method you control, how Sikuli should handle not found
		situations in this region.

		:param True: all subsequent find operations (explicit or
			implicit) will raise exception FindFailed (which is the default when a
			script is started) in case of not found.

		:param False: all subsequent find operations will not raise
			exception FindFailed. Instead, explicit find operations such as
			:py:meth:`Region.find` will return *None*. Implicit find operations 
			(action functions) such as :py:meth:`Region.click` will do
			nothing and return 0.

	.. py:method:: getThrowException()
	
		:return: ``True`` or ``False``

		Get the current setting as True or False (after start of script, this is True by
		default) in this region.

.. _GroupingMethodCallsWithRegion:

Grouping Method Calls ( with Region: )
--------------------------------------

.. index:: with

**Deprecated for 1.1.0+**
Instead of ``with`` use the new feature ``use(aRegionOrAScreen)``.

Instead of::

	# reg is a region object
	if not reg.exists(image1):
		reg.click(image2)
		reg.wait(image3, 10)
		reg.doubleClick(image4)

you can group methods applied to the same region using Python's ``with`` syntax::

	# reg is a region object
	with reg:
		if not exists(image1):
			click(image2)
		wait(image3, 10)
		doubleClick(image4)

All methods inside the ``with`` block (mind indentation) that have the region omitted are redirected to the
region object specified at the ``with`` statement.

**IMPORTANT Usage Note**

This only works without problems for region objects created on the script level using one of 
the constructors ``Region()``.

New region objects created with Region methods, that return new region objects, can only be used with ``with``, when casting 
the new region to a scripting level region object like this::

	# reg is a region object
	regNew = reg.left() # returns a non-scripting-level region object
	with Region(regNew):
		if not exists(image1):
			click(image2)
		wait(image3, 10)
		doubleClick(image4)
		
If you do not do that, you will get a syntax error saying, that ``__enter__`` is not defined for this region.
In all these cases use the casting approach using ``castedRegion = Region(regionNotWorking)`` (might also be needed with Match objects or window regions returned by the App class).
