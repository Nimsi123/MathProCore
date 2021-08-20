Folder structure
================

The diagram below outlines MathProgram's basic folder structure. The program is organized into
Primitives, Forms, and Classes as well as some miscellaneous items in DatabaseConnection.

| MathProgram
| ├── :ref:`QuestionReference`
| │ --  ├── :ref:`QuestionGeneratorReference`
| │ --  ├── helpers/
| │ --  │ --  ├── :ref:`ExprhReference`
| │ --  │ --  ├── :ref:`ImghReference`
| │ --  ├── :ref:`NumsReference`
| │ --  ├── :ref:`ImageReference`
| ├── :ref:`ClassesReference`
| │ --  ├── :ref:`DriversReference`
| │ --  ├── Algebra1
| │ --  ├── Algebra2
| │ --  ├── PreCalculus
| │ --  └── And More
| └── :ref:`UtilitiesReference`

In the folder structure above:

- ``MathProgram`` is the folder we get when we issue a ``git pull/clone`` command
- ``MathProgram/Question`` is the directory where the implementation of the QuestionGenerator class is stored.
- ``MathProgram/Classes`` is the directory where QuestionGenerators pertaining to specific classes (such as Algebra1, Algebra2, etc) are stored.
