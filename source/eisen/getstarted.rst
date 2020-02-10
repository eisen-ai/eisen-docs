*********************
Get Started
*********************

.. contents:: Table of Contents

Obtaining Eisen is easy. You can choose to set it up using pypi, sources or by getting a Docker image containing
a fully functional environment to develop with Eisen.

Once you have installed the software package, you can use it by simply importing it in your projects or by using the
command line interface (CLI).

In python:

.. code-block:: python

    import eisen

Using the CLI you need to type in your terminal:

.. code-block:: bash

    eisen --help

To receive futher information about how to use Eisen-CLI.

************************
Obtain Eisen via PyPi
************************

You can install Eisen using pypi by simply executing

.. code-block:: bash

   pip3 install --upgrade eisen

This will install all the sub packages of the Eisen project, including eisen-core and eisen-cli.

To install only Eisen-Core we suggest you execute

.. code-block:: bash

   pip3 install --upgrade eisen-core

If you want to be working on the latest possible software version, you can install from our GIT repository by executing

.. code-block:: bash

   pip3 install --upgrade git+https://github.com/eisen-ai/eisen-core.git


*****************************
Obtain Eisen via DockerHub
*****************************

You can obtain a Docker image from Dockerhub by executing, on a machine with a fully functional installation of
Docker CE

.. code-block:: bash

   TBA