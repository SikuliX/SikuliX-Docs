Working with masked images (ignoring parts of the image)
========================================================

.. versionadded:: 1.1.4

This tutorial demonstrates how you can ignore parts of images, that might vary in different scenarios (background,
inner parts of GUI elements, ...) in the search process of SikuliX.

We call it **Masking of Images**.

It comes in 3 different flavors (see :py:class:`Pattern`):

 - using images with 100% transparent parts (``auto-masked``)
 - saying, that the black parts of an image should be ignored (``black-masked``)
        ``Pattern(imageWithBlackParts).mask()``
 - using one of the above (auto-masked or black-masked) as the mask for another image (``image-masked``)
        ``Pattern(someImage).mask(imageWithBlackParts)``

.. sikulicode::

        # original image
        shot = "shot.png"

        # image with transparency (here shown as black, auto masked)
        shotTrans = "shotTransparent.png"

        # image with black parts useable as mask (black-masked)
        shotBlack = Pattern("shotBlack.png").mask()

        # image with black/white parts useable as mask
        shotMask = "shotMask.png"

        # Image masked with black/white image shotMask (image-masked)
        shotMasked = Pattern("shot.png").mask(shotMask)

        # find the search area
        # the image shot-tile-small.png must be visible on screen
        reg = None
        for match in findAllList(shot):
          if reg: reg = reg.union(match)
          else: reg = match
        if reg:
          reg = reg.grow(10)
          reg.highlight(2, "green")
        else:
          print "search area not visible"
          exit(-1)

        # a generalized function for this demonstration
        def search(image, descr = ""):
          print "##### searching for: (%s) %s" % (image, descr)
          matches = reg.findAllList(image)
          if len(matches) > 0:
            scoreMax = 0
            scoreMin = 1
            for match in matches:
              match.highlight()
              score = match.getScore()
              if score > scoreMax: scoreMax = score
              if score < scoreMin: scoreMin = score
            print "    found: %d matches score max/min: %.4f / %.4f" % (len(matches), scoreMax, scoreMin)
          else:
            print "    found: no matches"
          wait(2)
          highlightOff()

        # showcase original image
        search(shot, "original image")

        # showcase auto-masked
        search(shotTrans, "having transparency (auto masked)")

        # showcase black-masked
        search(shotBlack, "having black parts used as mask")

        # showcase image-masked
        search(shotMasked, "masked with other black/white image")

**This image must be visible on the screen, when running the example.**

.. image:: shot-tile-small.png
