**********************
Implementing Questions
**********************

This page will provide a thorough introduction to implementing questions using the :class:`QuestionGenerator` class.
A number of examples have been provided below that demonstrate different features of the :class:`QuestionGenerator` class.

------------------------------------------------------------------------------

Imports
*******

You must make some standard imports before you can implement a :class:`QuestionGenerator` object in your program.

.. code-block:: python

  from MathProgram.Question.question import QuestionGenerator
  from MathProgram.Question.nums import Relation, Interval
  from MathProgram.Question.image import *

------------------------------------------------------------------------------

The Basics
**********

The following is a possible implementation of a :class:`QuestionGenerator` class.

.. code-block:: python
  :linenos:

  # custom imports
  import sympy

  my_gen = QuestionGenerator(
      prompts = "What is the distance between the points ({a}, {b}) and ({c}, {d})?",
      solver = lambda a, b, c, d: sympy.simplify(
          sympy.sqrt((a-c)**2 + (b-d)**2)
      ),
      img = Graph([
        "(a, b)",
        "(c, d)"
      ]),
      constants = [
          Relation("a", Interval("Integer", 0, 10)),
          Relation("b", Interval("Integer", 0, 10)),
          Relation("c", Interval("Integer", 10, 20)),
          Relation("d", Interval("Integer", 10, 20)),
      ],
      pass_img = False,
      directions = "Use the distance formula to answer the question.",
      sub_topic = "Cartesian Geometry"
  )

prompts
-------

This parameter can be set to a *string* or a *list of strings*.
:class:`QuestionGenerator` can be passed a *prompts*, an :class:`Image`, or both.
At least one of these is required.

Sympy-parsable Expression Strings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

An expression string must be *sympy-parsable*. That is, taking away the quotes from an expression string
would leave behind a valid sympy object.

.. code-block:: python

  prompts = "5 * x + 2 = 0" # valid
  prompts = "sin(x) + 10 - x ** 2" # valid
  prompts = "x^2 - 10" # INVALID: the ^ symbol is not used by sympy.

Prompt Strings and Curly Braces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A prompt string is like an expression string except that it **can** include latex or text, unlike expression strings!

To add *text* to a prompt -- to create a word problem, for instance -- you must delimit expression strings in curly braces.
The string within "{" and "}" will be treated as a sympy object.

.. code-block:: python

  prompts = "Solve the equation {5 * x + 2 = 0}" # valid
  prompts = "Graph the equations {sin(x)} and {10 - x ** 2}" # valid
  prompts = "Factor the equation x ** 2 - 10" # INVALID: the expression string is not delimited.

Value placeholders
~~~~~~~~~~~~~~~~~~

A central point in generating question templates is the idea of value placeholders. Consider the example blow.

.. code-block:: python

  prompts = "y = (x - a) * (x - b)"

In this example, we can replace a and b with integers (see :class:`Relation` and :class:`Interval`).
The resulting question might look like "y = (x - 3)(x + 7)"

Adding latex
~~~~~~~~~~~~

If you wish to include *latex* in your prompt, you must delimit the latex expression with "$" symbols.
Note that latex is used to display math symbols! Unlike value placeholders, for example, latex often does not
take part in randomizing questions.

.. code-block:: python

  prompts = "Find the z-score if $\sigma = $ {a}, $\bar{x} = $ {b}, and $x = $ {c}." # valid

.. math::
  \text{Find the z-score if }\sigma =5 \text{, } \bar{x} = \text{3, and } x = 10.

img
---

To add images to the prompt or answer in a :class:`QuestionGenerator` you must make the following import.

.. code-block:: python

  from MathProgram.Question.image import *

Currently, the only available image type is the :class:`Graph`. To use :class:`Graph`, you must pass it
an *expression string* or a *list of expression strings*. Note that :class:`Graph` does not work with
prompt strings! You could alternatively pass sympy objects to :class:`Graph` instead of expression strings.

.. code-block:: python

  # example prompt
  prompt = "What is the distance between the points ({a}, {b}) and ({c}, {d})?"

  # valid image
  img = Graph([
    "(a, b)",
    "(c, d)",
  ]),

  # INVALID: We cannot include text in the expression string.
  img = Graph("The point (a, b)")

Additionally, if you only want a subset of your prompt passed to the solver, you can use the curly braces feature.
In the example below, the values of a and b are the only values passed to the solver. This can be useful in some cases.
For example, we wouldn't have to recompute the slope of the line below!

.. code-block:: python

  # valid image, note that 'a' will be passed to the solver.
  img = Graph("y = {a} * x + b")

solver
------

Every :class:`QuestionGenerator` must be passed a solver function.

Arguments
~~~~~~~~~

The **expression strings** found in the prompt and img arguments, **in that order**, are passed as sympy objects to the solver.
Note that prompt strings are never passed to the solver.

Return value(s)
~~~~~~~~~~~~~~~

The solver returns the solution to the question.
Valid solutions can take two forms. First, a sympy object or latex string. Second, an :class:`Image` object.
In the case that both are part of an answer, the solver can return both as a tuple.

Examples
~~~~~~~~

.. code-block:: python

  # example prompt
  prompt = "What is the distance between the points ({a}, {b}) and ({c}, {d})?"

  # example solver
  solver = lambda a, b, c, d: sympy.simplify(
      sympy.sqrt((a-c)**2 + (b-d)**2)
  )

  prompts = "5 * x + 2 = 0"

The solver can also be passed expression strings as sympy objects from images.

.. code-block:: python

  # example img
  img = Graph([
      "y = 100 * x + b",
      "y = {a} * x + {b}"
  ])

  # the solver will be passed 'y = 100 * x + b', 'a', and 'b' as sympy objects.
  solver = lambda equation, a, b: "\\text{The solution (in latex)!}"

  # valid solver that returns an image and a sympy object.
  def solver(equation, a, b):
      answer = a + b
      return Graph(equation), answer

constants
---------

Every :class:`QuestionGenerator` must be passed a list of :class:`nums.Relation` objects that
will determine the constant values generated in each question.
To create a constants list you must make the following import.

.. code-block:: python

  from MathProgram.Question.nums import Relation, Interval

To set the range of values for a given constant, you can use the :class:`nums.Interval` class.

.. code-block:: python

  # example prompt
  prompts = "a * x + 10"

  # will generate questions where 'a' is set to a value in the range [-10, 10].
  constants = [
      Relation("a", Interval("Integer", -10, 10))
  ]

You can also define relationships between constants using the :class:`nums.Relation` class.
To do this, you must pass an expression string to the 'expr' parameter.

.. code-block:: python

  # example prompt
  prompts = "a * x ** 2 + b * x + c"

  # will generate questions where 'c' is set 'a/b'.
  constants = [
      Relation("a", Interval("Integer", -10, 10)),
      Relation("b", Interval("Integer", -20, 20, exclude=[0])),
      Relation("c", expr="a/b")
  ]

If you are unsure about what the dependent and independent constants in your system are
you can define an :class:`Interval` and *expr* for each relation. The system will be solved in the :class:`QuestionGenerator`.

.. code-block:: python

  # example prompt
  prompts = "a * x ** 2 + b * x + c"

  # the system will be solved in the QuestionGenerator.
  constants = [
      Relation("a", Interval("Integer", -10, 10), expr="3 - b"),
      Relation("b", Interval("Integer", -20, 20), expr="2 - c"),
      Relation("c", Interval("Integer", -10, 10), expr="a + b")
  ]

Note that Intervals are not limited to Integer domains.
For more information on the :class:`Relation` and :class:`Interval` classes see :ref:`NumsReference`.

directions and sub_topic
------------------------

- **directions**: A :class:`QuestionGenerator` may optionally be passed directions as a latex string. All questions generated by
  this :class:`QuestionGenerator` follow these directions. Only in rare cases do you not pass a *directions* argument.

- **sub_topic**: We group questions by their *sub_topics*. Questions with different directions can be placed in the same
  sub_topic. For example, a sub_topic for "Graphing Quadratic Functions" can have questions with directions "Graph the vertex."
  or "Describe the end behavior." It is common for QuestionGenerators to share the same sub_topic.

Flags
-----

:class:`QuestionGenerator` classes also have a number of optional flags.

- **pass_img**: A boolean flag that determines if img expressions will be passed to the solver. Defaults to True.

- **permute**: A boolean flag that determines if the expression/equation should be algebraically shuffled (currently only supported over addition). Note that the expression is mathematically equivalent to any of its permutations.

------------------------------------------------------------------------------

Using Images
************

There are two options for setting the window viewing parameters for Graph objects:
using the pre-defined autofit feature, and focusing in on a point.

show
----

Depending on what you are graphing, there is a chance that we have already written
a function to determine the viewing bounds for your graph. Currently, we support
the show command for the following arguments:

- intercepts
- roots
- quadratic-vertex
- absval-vertex

Consider the following example.

.. code-block:: python

    QuestionGenerator(
        prompts= "y = {a*x**2+b*x+c}",
        solver=lambda p: Graph(p, show = "quadratic-vertex"),
        constants=[
          Relation("a", Interval("Integer", 1, 4, exclude=[0])),
          Relation("b", Interval("Integer", -8, 8, exclude=[0])),
          Relation("c", Interval("Integer", -15, 15, exclude=[0])),
        ],
        directions="Graph the function.",
        sub_topic="Graph quadratic functions in standard form"
    )

In this :class:`QuestionGenerator`, we center the graph window around the vertex of a parabola.
You can use the show command for prompt images and answer images.

Consider the next example where we use the show command for a prompt image.

.. code-block:: python

    QuestionGenerator(
            img = Graph("y = (x - a) * (x - b)", show = "roots"),
            solver=solver,
            constants=[
                Relation("a", Interval("Integer", -5, 5)),
                Relation("b", Interval("Integer", -5, 5)),
            ],
            sub_topic = "Find the roots of a graphed polynomial."
    )

Here, we use the show command to center a function around its roots.

focus
-----

The focus command requires more detail than the *show* command. Please use the *show* command
over the focus command as much as possible.

You must pass a dictionary with three keys: `center`, `hdisp`, `vdisp`. The graph image will be centered at the tuple stored at `focus[‘center’]`, with horizontal displacement `focus[‘hdisp’]` and vertical displacement `focus[‘vdisp’]`.

The values, which are simply expression strings, can use the value placeholders from the prompts!

In the example below, we center the window at the vertex, and we have 5 units for the horizontal displacement, and `b` units
for the vertical displacement.

.. code-block:: python

  question_list.append(
      QuestionGenerator(
          prompts="x = (y - a) * (y - b)",
          solver=lambda eq: Graph(eq, focus = {
            "center": ["a", "b"],
            "hdisp": "5",
            "vdisp": "b/5 - 3"
          }),
          constants=[
              Relation("a", Interval("Integer", -5, 5)),
              Relation("b", Interval("Integer", -5, 5)),
          ],
          directions="Graph the parabola and label the vertex.",
          sub_topic="Graphing parabolas"
      )
  )

------------------------------------------------------------------------------

Examples
********

Below are a number of example :class:`QuestionGenerator` objects that demonstrate the the complete
functionality of the class.

.. list-table::
   :widths: 50 50 50 50
   :header-rows: 1

   * -
     - Latex Answer
     - Image Answer
     - Latex & Image Answer
   * - **Latex Prompt**
     - :ref:`ExampleAReference`
     - :ref:`ExampleBReference`
     - :ref:`ExampleCReference`
   * - **Word Problem Prompt**
     - :ref:`ExampleDReference`
     - :ref:`ExampleEReference`
     - :ref:`ExampleFReference`
   * - **Image Prompt**
     - :ref:`ExampleGReference`
     - :ref:`ExampleHReference`
     - :ref:`ExampleIReference`
   * - **Word Problem & Image Prompt**
     - :ref:`ExampleJReference`
     - :ref:`ExampleKReference`
     - :ref:`ExampleLReference`

.. _ExampleAReference:

Example A
---------

.. code-block:: python

  # A question generator where the prompt and answer are both latex:
  gen =	QuestionGenerator(
      prompts = "a * Abs(b * x - c) = d",
      solver = lambda prmpt: sympy.solveset(prmpt, sympy.symbols("x"), domain = sympy.S.Reals),
      constants = [
          Relation(letter, Interval("Integer", 1, 4)) for letter in "abcd"
      ],
      permute = True,
      directions = "Solve the absolute value equation.",
      sub_topic = "Solving Absolute Value functions."
  )

.. _ExampleBReference:

Example B
---------

.. code-block:: python

  # A question generator where the prompt is latex and the answer is an image:
  gen = QuestionGenerator(
      prompts = "y = a * Abs(b * x - c) - d",
      solver = lambda prompt: Graph(prompt, show = "absval-vertex"),
      constants = [
          Relation(letter, Interval("Integer", 1, 4)) for letter in "abcd"
      ],
      permute = True,
      directions = "Graph the absolute value equation.",
      sub_topic = "Graphing Absolute Value functions."
  )

.. _ExampleCReference:

Example C
---------

.. code-block:: python

  # A question generator where the prompt is latex and the answer is both image and latex:
  def solver(eq):
      x, y = sympy.symbols('x y')
      graph = Graph(eq, show = "intercepts")
      solution = sympy.solve(eq.subs(y, 0), x)
      if len(solution) == 1:
          return f"x = {solution[0]}", graph
      return f"x = {solution[0]}, x = {solution[1]}", graph

  gen = QuestionGenerator(
      prompts = ["y = (x - a) * (x - b)", "y = x*(x - a)"],
      solver=solver,
      constants=[
          Relation("a", Interval("Integer", -5, 5, exclude = [0])),
          Relation("b", Interval("Integer", -5, 5)),
      ],
      sub_topic = "Graph the parabola and label its roots."
  )

.. _ExampleDReference:

Example D
---------

.. code-block:: python

  # A question generator where the prompt is a word problem and the answer is latex:
  def solver(a, b):
      x = sympy.symbols("x")
      return sympy.expand((x - a) * (x - b))

  gen = QuestionGenerator(
      prompts = "A quadratic function has zeros {a} and {b}. Write its equation in standard form.",
      solver = solver,
      constants = [
          Relation("a", Interval("Integer", -10, 10, exclude=[0])),
          Relation("b", Interval("Integer", -10, 10, exclude=[0])),
      ],
      sub_topic = "Write a quadratic function from its zeroes"
  )

.. _ExampleEReference:

Example E
---------

.. code-block:: python

  # A question generator where the prompt is a word problem and the answer is an image:
  from MathProgram.Question.nums import radians

  def solver(a, b, c, d):
      eq = f"y = {a} * sin({b} * x + {c}) + {d}"
      return Graph(eq, xAxisStep = "pi/2")

  gen = QuestionGenerator(
      prompts = [
          "Vertical stetch of {a}, horizontal stretch of {b}, horizontal translation to the right of {c}, \
           vertical translation upwards of {d}",
          "Vertical compression of {1/a}, horizontal compression of {1/b}, horizontal translation to the \
           left of {-c}, vertical translation downwards of {-d}"
      ],
      solver = solver,
      constants = [
          Relation("a", Interval("Integer", 1, 4)),
          Relation("b", Interval("Integer", 1, 4)),
          Relation("c", radians),
          Relation("d", Interval("Integer", 1, 4))
      ],
      directions = "Graph the translations from the parent function $y = \\sin(x)$.",
      sub_topic = "Graph translations of $\\sin$ functions."
  )

.. _ExampleFReference:

Example F
---------

.. code-block:: python

  # A question generator where the prompt is latex and the answer is both image and latex:
  def solver(a, b, c, d):
      x, y = sympy.symbols("x y")
      slope = sympy.sympify(f"({d} - {b}) / ({c} - {a})")
      if slope == sympy.zoo:
          eq = sympy.Eq(x, a)
      else:
          eq = sympy.Eq(y - b, slope * (x - a))
      x_int = sympy.solve(eq.subs(y, 0), x)
      y_int = sympy.solve(eq.subs(x, 0), y)

      def interpret_intercepts(vals, intercept):
          if len(vals) == 0:
              return "\\text{There are no " + intercept + "-intercepts. }"
          if len(vals) == 1:
              ints = sympy.latex(vals[0])
          else:
              ints = ", ".join(sympy.latex(v) for v in vals)
          return "\\text{The " + intercept + "-intercept is at }" + ints + "."

      intercepts = ""
      intercepts += interpret_intercepts(x_int, "x")
      intercepts += interpret_intercepts(y_int, "y")
      return intercepts, Graph(eq, show = "intercepts")

    gen = QuestionGenerator(
        prompts = "Sketch a line between ({a}, {b}) and ({c}, {d}). What are its x and y-axis intercepts?",
        solver = solver,
        constants=[
            Relation("a", Interval("Integer", -5, 5, exclude=[0])),
            Relation("b", Interval("Integer", -5, 5, exclude=[0])),
            Relation("c", Interval("Integer", -5, 5, exclude=[0])),
            Relation("d", Interval("Integer", -5, 5, exclude=[0]))
        ],
        sub_topic = "Graph a line between two points and label the axis intercepts"
    )

.. _ExampleGReference:

Example G
---------

.. code-block:: python

  # A question generator where the prompt is an image and the answer is latex:
  def solver(eq):
      x, y = sympy.symbols('x y')
      solution = sympy.solve(eq.subs(y, 0), x)
      if len(solution) == 1:
          return f"x = {solution[0]}"
      return f"x = {solution[0]}, x = {solution[1]}"

  gen = QuestionGenerator(
      img = Graph("y = (x - a) * (x - b)", show = "roots"),
      solver=solver,
      constants=[
          Relation("a", Interval("Integer", -5, 5)),
          Relation("b", Interval("Integer", -5, 5)),
      ],
      sub_topic = "Find the roots of a graphed polynomial."
  )

.. _ExampleHReference:

Example H
---------

.. code-block:: python

  # A question generator where the prompt and answer are both images:
  def solver(expr):
  	new_eq = sympy.Eq(expr.lhs, expr.rhs * -1)
  	return Graph(new_eq)

  gen = QuestionGenerator(
      img = Graph("y = (x - a) * (x - b)"),
      solver=solver,
      constants=[
          Relation("a", Interval("Integer", -5, 5, exclude=[0])),
          Relation("b", Interval("Integer", -5, 5, exclude=[0]))
      ],
      sub_topic="Sketch the quadratic reflected across the x-axis"
  )

.. _ExampleIReference:

Example I
---------

.. code-block:: python

  # A question generator where the prompt is an image and the answer is both image and latex:
  def solver(a, b):
    	x, y = sympy.symbols("x y")
    	eq = sympy.Eq(y, a)
    	return eq, Graph(eq)

  gen = QuestionGenerator(
      img = Graph("y = {a} * x + {b}"),
      solver=solver,
      constants=[
          Relation("a", Interval("Integer", -5, 5, exclude=[0])),
          Relation("b", Interval("Integer", -5, 5, exclude=[0]))
      ],
      sub_topic="Find the graphed function's derivative and sketch it"
  )

.. _ExampleJReference:

Example J
---------

.. code-block:: python

  # A question generator where the prompt is both image and latex and the answer is latex:
  gen = QuestionGenerator(
      prompts = "Write the equation of the function after translating the graphed function to the left by {a}.",
      img = Graph(
          "y = (x - {h}) ** 2 + {k}",
          focus = {"center": ["h", "k"], "hdisp": "10", "vdisp": "10"}
      ),
      solver = lambda a, h, k: "y = " + sympy.latex(sympy.sympify(f"(x - {h} + {a}) ** 2 + {k}")),
      constants = [
          Relation("a", Interval("Integer", 1, 4)),
          Relation("h", Interval("Integer", -5, 5, exclude=[0])),
          Relation("k", Interval("Integer", -10, 10, exclude=[0])),
      ],
      sub_topic = "Transform a quadratic function"
  )

.. _ExampleKReference:

Example K
---------

.. code-block:: python

  # A question generator where the prompt is both image and latex and the answer is an image:
  gen = QuestionGenerator(
      prompts = "Graph the function after translating the graphed function to the left by {a}.",
      img = Graph(
          "y = (x - {h}) ** 2 + {k}",
          focus = {"center": ["h", "k"], "hdisp": "10", "vdisp": "10"}
      ),
      solver = lambda a, h, k: Graph(
          f"y = (x - {h} + {a}) ** 2 + {k}",
          xAxisStep = "1",
          focus = {"center": [f"{h} - {a}", f"{k}"], "hdisp": "10", "vdisp": "10"}
      ),
      constants = [
          Relation("a", Interval("Integer", 1, 4)),
          Relation("h", Interval("Integer", -5, 5, exclude=[0])),
          Relation("k", Interval("Integer", -10, 10, exclude=[0])),
      ],
      sub_topic = "Transform and Graph a quadratic function"
  )

.. _ExampleLReference:

Example L
---------

.. code-block:: python

  # A question generator where the prompt is both image and latex and the answer is both image and latex:
  def solver(a, h, k):
      ans = "y = " + sympy.latex(sympy.sympify(f"(x - {h} + {a}) ** 2 + {k}"))
      img_ans = Graph(
          f"y = (x - {h} + {a}) ** 2 + {k}",
          xAxisStep = "1",
          focus = {"center": [f"{h} - {a}", f"{k}"], "hdisp": "10", "vdisp": "10"}
      )
      return ans, img_ans

  gen = QuestionGenerator(
      prompts = "Write the equation of and graph the function after translating the graphed function to the left by {a}.",
      img = Graph(
          "y = (x - {h}) ** 2 + {k}",
          focus = {"center": ["h", "k"], "hdisp": "10", "vdisp": "10"}
      ),
      solver = solver,
      constants=[
          Relation("a", Interval("Integer", 1, 4)),
          Relation("h", Interval("Integer", -5, 5, exclude=[0])),
          Relation("k", Interval("Integer", -10, 10, exclude=[0])),
      ],
      sub_topic="Transform and Graph quadratic function"
  )

------------------------------------------------------------------------------

Special Cases
*************

- degrees

- imaginary numbers
