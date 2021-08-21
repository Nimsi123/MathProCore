.. _QuestionReference:

Question
========

Question contains the implementation of the :class:`question.QuestionGenerator` class, 
along with accessory classes like Graph, Interval, etc.

.. _QuestionGeneratorReference:

question.py
***********

.. autoclass:: src.mathprocore.Question.question.QuestionGenerator
    :show-inheritance:

.. _NumsReference:

nums.py
*******

.. autoclass:: src.mathprocore.Question.nums.Relation
    :show-inheritance:

.. autoclass:: src.mathprocore.Question.nums.Interval
    :show-inheritance:

The examples below demonstrate how to represent mathematical intervals using the :class:`Interval` class.

.. code-block:: python

  # This interval will contain integers between -10 and 10 (inclusive).
  inter = Interval("Integer", -10, 10)

  # You can use the zero keyword to prevent zero from being contained in the interval.
  inter = Interval("Integer", -10, 10, exclude=[0])

  # This interval will contain arbitrary floating point numbers between 0 and 1.
  inter = Interval("Real", 0, 1)

  # Both of these intervals will contain integers between 0 and 100 (inclusive).
  inter = Interval("Natural", 0, 100)
  inter = Interval("Natural", -100, 100)

  # This interval contains rational numbers.
  inter = Interval("Rational", -1/2, 1/2)

  # This interval only contains the numbers 5, 17, and 32.
  inter = Interval("Finite", [5, 17, 32])

  # You can use the Finite keyword to make an interval of special angles.
  angles = Interval("Finite", [0, sympy.pi / 2, sympy.pi])

  # You can also specify the units of an interval (the only option at the moment is degrees).
  angles = Interval("Real", 0, 360, units="degrees")

.. _ImageReference:

image.py
********

.. autoclass:: src.mathprocore.Question.image.Image
    :show-inheritance:
    :members:

.. autoclass:: src.mathprocore.Question.image.Graph
    :show-inheritance:


helpers
*******

.. _ExprhReference:

exprh.py
^^^^^^^^
.. automodule:: src.mathprocore.Question.helpers.exprh
    :members:
    :show-inheritance:

.. _ImghReference:

imgh.py
^^^^^^^
.. automodule:: src.mathprocore.Question.helpers.imgh
    :members:
    :show-inheritance: