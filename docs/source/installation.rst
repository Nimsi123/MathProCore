Installation
============

1) Install git to your machine `here <https://git-scm.com/downloads>`_.
2) Open to your Desktop directory through the command line.
3) Only complete this step if you have not used git before. In the command line, type:
	``git config --global user.name “Put your user name here”``

	``git config --global user.email “Put your email here”``
4) Make sure your directory is the MathProgram folder. To clone the repository into the Desktop directory, run:
	``git clone https://github.com/Nimsi123/MathProgram.git``
5) To install MathProgram dependencies, run:
	``pip install -r requirements.txt``


Put the folder `MathProgram/` in the same directory as your source files.
To find the source files directory, see the code block below.

.. code-block:: python
  :linenos:

  """
  Run the following code to identify the directory where your source files are stored.
  """

  import sympy
  print(sympy.__file__)

To test whether you have the setup correct, run the following script.

.. code-block:: python
  :linenos:

  from MathProgram import *