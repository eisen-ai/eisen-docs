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


*****************************
Contributint to Eisen
*****************************

Eisen is open to contributions by the community. Anyone can contribute to Eisen and we are constantly working to
reduce the number of guidelines that need to be followed when building it.

The truly important aspect is to keep functionality organized in many small composible blocks. The blocks should be
readable, easily understandable, documented (possibly) and tested (possibly). If you decide to contribute, open a
pull request on GitHub and we will be happy to help you and fill in the blanks for you, if necessary.

Implementing Transforms
=============================

If you want to build a transform, remember, keep it simple and give it just one specific functionality. If you can
imagine its functionality being split in different stages, split them up and implement each stage as one transform.

When building a transform try to follow this example:

.. code-block:: python

    class MyTransform:
        def __init__(self, fields, param1, param2):
            self.fields = fields  # this is usually a list of fields
            self.param1 = param1  # a parameter
            self.param2 = param2  # another parameter

        def __call__(self, data):
            # data is always a dictionary
            for field in self.fields:
                current_data = data[field]
                # more processing on current_data
                # ...

                data[field] = current_data

            return data

Look at the documentation and the source code of Eisen-Core to learn more about transformation. Start easy and
remember to keep the functionality of each transform minimal, so that you end up with a bunch of stackable
blocks.

Implementing Datasets
=============================

If you want to contribute a Dataset, remember to make sure that it follows the rules of PyTorch Datasets. In Eisen
data is always a list of dictionaries (that will be passed as individual dictionaries to the various transforms).
Therefore the `__getitem__` method returns a dictionary having certain keys. Moreover, the __init__ method of a
dataset in Eisen has a customary (but not completely mandatory) parameter data_dir which usually contains the root
path of the dataset being read by the Dataset object.

When building a Dataset you should try to follow this example:

.. code-block:: python

    from torch.utils.data import Dataset

    class MyDataset(Dataset):
        def __init__(self, data_dir, param1, param2):
            super(MyDataset, self).__init__()

            self.data_dir = data_dir  # this is usually a list of fields
            self.param1 = param1  # a parameter
            self.param2 = param2  # another parameter

            # more processing here
            # ...

            self.datalist = result_of_processing

        def __len__(self, data):
            return len(self.datalist)

        def __getitem__(self, idx):
            return {'data': self.datalist[idx]}

Implementing I/O Transforms
===============================

ToDo


Implementing Modules
===========================

Models, layers, losses and metrics are Modules (derived from class `torch.nn.Module`) in Eisen. They can be implemented
as any other `Module` in pytorch. When used into Eisen, they need to be wrapped. This is necessary as Eisen workflows
will pass data to these modules in form of dictionaries (`**kwargs`) and expect their output in form of dictionaries as
well.

The aforementioned usage detail is not relevant when implementing a new Model, loss, metric or layer. Follow this
example to implement a Module compatible with Eisen:

.. code-block:: python

    from torch.nn import Module

    class MyModule(Module):
        def __init__(self, param1, param2):
            super(MyModule, self).__init__()

            self.param1 = param1  # a parameter
            self.param2 = param2  # another parameter

            # more processing here
            # definition of layers etc
            # ...

        def forward(x):
            # some computation with layers of module
            # results are stored in the variable "results"

            return results


Implementing Workflows
===========================

ToDo