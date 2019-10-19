.. _tutorialMasking:

Working with masked images (ignoring parts of the image) # 1.1.4
================================================================

.. versionadded:: 2.0.0

This tutorial demonstrates how you can ignore parts of images, that might vary in different scenarios (background,
inner parts of GUI elements, ...) in the search process of SikuliX.

We call it **Masking of Images**.

It comes in 3 different flavors (see :py:class:`Pattern`):

 - using images with 100% transparent parts (``auto-masked``)
 - saying, that the black parts of an image should be ignored (``black-masked``)
        ``Pattern(imageWithBlackParts).mask()``
 - using an auto-masked or black-masked image as the mask (``image-masked``)
        ``Pattern(someImage).mask(imageWithBlackParts)``

.. sikulicode::

        # original image
        shot = "shot.png"

        # image with transparency (auto-masked)
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
        search(shotTrans, "transparency (auto-masked)")

        # showcase black-masked
        search(shotBlack, "black parts as mask (black-masked")

        # showcase image-masked
        search(shotMasked, "masked with other image (image-masked)")

**This image must be visible on the screen, when running the example.**

.. image:: shot-tile-small.png

... and this is the output you should get principally::

        ##### searching for: (shot.png) original image
            found: 6 matches score max/min: 0.9971 / 0.9971
        ##### searching for: (shotTransparent.png) transparency (auto-masked)
            found: 6 matches score max/min: 0.9907 / 0.9900
        ##### searching for: (P(shotBlack.png) S: 0.7 masked) black parts as mask (black-masked
            found: 6 matches score max/min: 0.9985 / 0.9983
        ##### searching for: (P(shot.png) S: 0.7 masked) masked with other image (image-masked)
            found: 6 matches score max/min: 0.9985 / 0.9983

