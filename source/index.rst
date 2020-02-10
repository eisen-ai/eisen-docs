.. Eisen documentation master file, created by
   sphinx-quickstart on Wed Jan 29 20:11:11 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Eisen Documentation
=================================
Eisen is a software package to enable simple and quick development and experimentation with
deep learning models. It is designed for applications in healthcare.

Installing Eisen is extremely simple.

.. code-block:: bash

   pip3 install --upgrade eisen

Eisen implements an opinionated API that builds directly on PyTorch. The goal of Eisen is to be:

- Extremely simple to use

- Extremely easy to understand

- Similar to other packages in the PyTorch echosystem

- Easy to contribute to

We have subdivided eisen in several sub-packages:

- Eisen meta-package (installs everything) `pip3 install eisen`

- Eisen core `pip3 install eisen-core`

- Eisen CLI `pip3 install eisen-cli`

You can also obtain Eisen as a Docker image which can be downloaded from dockerhub `(TBA)`


Links
==================

.. toctree::
   :caption: Home
   :maxdepth: 4

   index

.. toctree::
   :caption: Eisen-Core API
   :maxdepth: 4

   eisen/api


.. toctree::
    :caption: Get Started
    :maxdepth: 4

    eisen/getstarted

.. toctree::
    :caption: Tutorials
    :maxdepth: 4

    eisen/tutorials

.. toctree::
    :maxdepth: 4
    :caption: Features

    eisen/features


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`






