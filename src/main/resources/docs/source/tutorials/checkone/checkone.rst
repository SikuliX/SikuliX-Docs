Check the Right Checkbox
========================

This tutorial demonstrates how to check a particular checkbox, when there are
multiple checkboxes in a window. Consider the following window, which is the window
for setting the preferences for ``Dock on Mac``, and we want to check the checkbox
indicated by the circle that reads "Minimize Windows into Application Icons."

.. image:: scenario.png
	:width: 550

Simply clicking on the image of the checkbox like below will not work.

.. sikulicode::

	click("checkbox.png")

Sikuli does not know which checkbox we are referring to. It simply clicks on the
first one it finds.

**The solution:** identify the checkbox with the describing text on the right side

First we want to obtain the region the ``Dock pane`` occupies on the screen. One
way to accomplish this is to use a spatial operator to obtain a region below the
title bar of the ``Dock pane``.

.. image:: find_title_and_below.png
	:width: 550

The Sikuli script to do this is:

.. sikulicode::

	reg = find("title.png").below(300)

It finds the title bar and then defines the region 300 pixels below, using
the spatial operator :py:meth:`below <Region.below>`. The resulting region is
assigned to the variable ``reg``, which is the orange rectangle in the figure above.

Next, we can search within the region ``reg`` for the label text of the checkbox
we want to check and click on it.

.. sikulicode::

	labelText = reg.find("label.png")
	click(labelText)

Uuups - now Sikuli will click on the center of the label. However, what
we want is to click the check box on the left of the label.

Sikuli IDE provides a convenient dialog for specifying where to click relative to
the center of a pattern. This is known as the ``target offset``.
The dialog can be activated simply by clicking on the
thumbnail image in the IDE.

.. image:: interface_offset.png
	:width: 500

We can click on the checkbox to indicate the desired location of
the click point. In this example, the offset is then determined to be -137 in x,
which means 137 pixels to the left of the center of the text label.  After confirming
the offset, the thumbnail in the script editor will be updated showing a small red
cross (click point) now at the far left.

.. sikulicode::

	labelText = reg.find("label_offset.png")
	click(labelText)

Now ``click(labelText)`` will do the right thing and click on the checkbox
instead of the center of the text label.




