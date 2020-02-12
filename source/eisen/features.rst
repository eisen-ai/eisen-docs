*********************
Features
*********************

.. contents:: Table of Contents

Eisen is built to give simple access to deep learning in medical image analysis. The main features are:

* Support for common datasets such as MSD, PatchCamelyon, etc.
* Pre-built transformations to manipulate data
* Training, Validation and Testing loops
* Automatic logging and summary export
* Visual experiment building through visual UI at http://builder.eisen.ai
* Compatibility with other libraries such as Torchvision and user code
* Different ways of accessing functionality, module import or CLI


Accessing Eisen Functionality
================================

As stated above, it is possible to use eisen as a library or via a command line interface. Usage with command line
interface requires experiment configuration which shall be supplied in form of a JSON file. Creating this JSON file
manually is often tedious and is suggested only in case the user has special needs (Eg. needs to include own python
modules into the experiments and make use of advanced functionality). The JSON configuration file can also be created
via a web-based user interface that can be reached at http://builder.eisen.ai

.. |logo1| image:: static/gif_build.gif
    :scale: 50%

.. |logo2| image:: static/gif_code.gif
    :scale: 50%

.. table:: Here you can see a visual comparison between the two ways of using Eisen.
   :align: center

   +---------+---------+
   | |logo1| | |logo2| |
   +---------+---------+