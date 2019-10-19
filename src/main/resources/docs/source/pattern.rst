Pattern
=======

.. py:class:: Pattern

A pattern is used, to associate an image file with additional attributes used in find
operations and when acting on a match object.

**Minimum Similarity:** 

While using a :py:meth:`Region.find` operation, 
if only an image file is provided, Sikuli searches
the region using a default minimum similarity of 0.7.
This default value can be changed in :py:attr:`Settings.MinSimilarity`.

Using :py:meth:`similar() <Pattern.similar>` you can associate a specific similarity
value, that will be used as the minimum value, when this pattern object is searched. 
The IDE supports adjusting the minimum similarity of captured images using the Preview Pane
(internally in the script, the images are turned into a pattern object automatically).

**Click Point:**

Normally when clicking on a match, the center pixel of the associated
rectangle is used. With a pattern object, you can define a different click point 
relative to the center using :py:meth:`targetOffset() <Pattern.targetOffset>`.

.. versionadded:: 2.0.0

**Masking:**

Masking with SikuliX image search means, that the corresponding pixels in the image, that have a 0 in the mask pixel,
will be ignored during search. Masks are internally created from given images having either
black parts (masked on request) or transparency (masked automatically).

Images having set some 100% transparency in the PNG-alpha-channel will always be treated as masked images so that
the transparent parts are ignored during the search.

Here we are talking about the cases,

where you want an image having black parts to be treated as masked (:py:meth:`Pattern.mask()`)::

        maskImg = Pattern(someImage).mask()

or that you want an image (having black or transparent parts) to be used as mask for another image (:py:meth:`Pattern.mask()`)::

        pImg = Pattern(someOtherImage).mask(ImageOrPattern)

Be aware: in the latter case, both base image and mask image must have the same size in pixels.

.. py:class:: Pattern

  .. py:method:: Pattern(string)

    :param string: a path to an image file
    :return: the pattern object

    This will initialize a new pattern object without any additional attributes.
    As long as no pattern methods are used additionally, it is the same as just
    using the image file name itself in the find operation.

  .. py:method:: similar(similarity)

    Set the minimum similarity of the given Pattern object to the specified value.

    :param similarity: the minimum similarity to use in a find operation. The value should be between 0 and 1.
    :return: the pattern object

  .. py:method:: exact()

    Set the minimum similarity of the given Pattern object to 0.99, which means exact match is required.

    :return: the pattern object

  .. versionadded:: 1.1.3

  .. py:method:: resize(factor)

    A decimal value greater 0 and not equal to 1 to switch the feature on.

    With this setting you can tell SikuliX to resize the given image before a search operation using the given factor,
    which is applied to both width and height.
    The implementation internally uses the standard behavior of resizing a Java-AWT-BufferedImage.
    See also: :py:attr:`Settings.AlwaysResize`

    To switch the feature off again, just assign 0 or 1.

    :param factor: a decimal value
    :return: the pattern object

  .. py:method:: targetOffset(dx, dy)

    For the given Pattern object define a click offset. By default, the click point is the center of the found match. By setting the target offset, it is possible to specify a click point other than the center. *dx* and *dy* will be used to calculate the position relative to the center.

    :param dx: x offset from the center
    :param dy: y offset from the center
    :return: the pattern object

  .. py:method:: getFilename()

    Get the filename of the image contained in the Pattern object.

    :return: a filename as a string

  .. py:method:: getTargetOffset()

    Get the target offset of the Pattern object.

    :return: a :py:class:`Location` object as the target offset

    .. versionadded:: 2.0.0

  .. py:method:: mask([imageOrPattern])

    :param imageOrPattern: image filename, Image or Pattern

    If a mask can be derived from imageOrPattern (image has transparent or black parts or is a Pattern with a mask),
    then it will become the mask for this pattern.

    If ``imageOrPattern`` is omitted: The pattern will be treated as masked based on black parts of the image.

    :return: the modified pattern

