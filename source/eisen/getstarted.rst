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


Obtain Eisen via PyPi
================================

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


Obtain Eisen via DockerHub
================================

You can obtain a Docker image from Dockerhub by executing, on a machine with a fully functional installation of
Docker CE. You can change the `latest` tag to an Eisen version to download a specific version of Eisen.

.. code-block:: bash

   docker pull eisenai/eisen:latest


*****************************
Train your first model
*****************************

A collection of tutorials in form of Colab notebook that you can execute on free GPUs sponsored by Google Colab
is available at http://docs.eisen.ai/eisen/tutorials.html

Snippets from the code implementing volumetric medical image segmentation on the
Medical Segmentation Decathlon (MSD) dataset be found here. The full example is available here: http://bit.ly/2HjLlfh.


Dataset and Transforms
================================

Assuming you have imported the necessary sub packages from Eisen and PyTorch, we start from instantiating a
Medical Segmentation Decathlon dataset which allows us to work with the MSD data within Eisen, together with
image loading and manipulation transforms that are necessary to make the data suitable for training.


.. code-block:: python

   # readers: one for the images, one for the labels
    read_tform = LoadNiftyFromFilename(['image', 'label'], PATH_DATA)

    # image manipulation transforms

    resample_tform = ResampleNiftiVolumes(
        ['image', 'label'],
        [1.0, 1.0, 1.0],
        'linear'
    )

    to_numpy_tform = NiftiToNumpy(['image', 'label'])

    crop = CropCenteredSubVolumes(fields=['image', 'label'], size=[64, 64, 64])

    add_channel = AddChannelDimension(['image', 'label'])

    map_intensities = MapValues(['image'], min_value=0.0, max_value=1.0)

    threshold_labels = ThresholdValues(['label'], threshold=0.5)


    # create a composed transform to manipulate and load data
    tform = Compose([
        read_tform,
        resample_tform,
        to_numpy_tform,
        crop,
        map_intensities,
        threshold_labels,
        add_channel,
    ])

    # create a dataset from the training set of the MSD dataset
    dataset = MSDDataset(
        PATH_DATA,
        NAME_MSD_JSON,
        'training',
        transform=tform
    )

Eisen uses transforms similar to those employed by other packages belonging to the PyTorch universe.
The only difference is that Eisen represents data as dictionaries. Transform operate on dictionaries and
are able to add, remove or change dictionary fields. Moreover, the transforms have visibility over all the
dictionary fields belonging to a data-point, so they can make computations considering multiple aspects of the data.

For example, the NiftiToNumpy transform shown above, changes the nature of the data stored in correspondence of
the filed 'image' and 'label' of the data dictionary, converting the relative data into Numpy. The transform MapValues
changes the range of the values of the data stored at 'image'. And so on.

In this way, once we compose together the transforms and obtain a transform chain, we can create very complex
transformation functions from basic and well tested building blocks. Moreover, we are not limited in flexibility by
their interface, since transforms will always be called on a data dictionary which can have an arbitrary number of
fields and carry arbitrary information. Transform calls return dictionaries as well.

Workflow components
================================

Next we declare the building blocks of our training, namely Model, Metrics, Losses, and Optimizer.

.. code-block:: python

    # specify model and loss (building blocks)

    model = EisenModuleWrapper(
        module=VNet(input_channels=1, output_channels=1), input_names=['image'], output_names=['predictions']
    )

    loss = EisenModuleWrapper(module=DiceLoss(), input_names=['predictions', 'label'], output_names=['dice_loss'])

    metric = EisenModuleWrapper(module=DiceMetric(), input_names=['predictions', 'label'], output_names=['dice_metric'])

    optimizer = Adam(model.parameters(), 0.001)


This part is indeed quite self explanatory. The only puzzling particularity could be represented by the use of
EisenModuleWrapper object. The purpose of EisenModuleWrapper is to indicate how information from the data dictionary
should be routed to the various modules. It also indicates what information should be added as outputs of these
modules are computed.

Let's take the first declaration making use of EisenModuleWrapper. We wrap a VNet object, and we indicate that
the fields `['image']` should be taken from each batch and routed to the (only) input this network has.
We also indicate that the only output of the network, should be routed to a `['predictions']` field.

The loss is also wrapped, it has two inputs, which will take - in order - the content of `predictions` and `label`
to produce an output `dice_loss`.

This may seem complicated, but it gives us the ability of using ANY network written in pytorch, from any code base
and any purpose within Eisen by simply specifying how routing should be done.


Workflow
================================

At this point we declare a workflow:

.. code-block:: python

    # join all blocks into a workflow (training workflow)
    training_workflow = Training(
          model=model,
          losses=[loss],
          data_loader=data_loader,
          optimizer=optimizer,
          metrics=[metric],
          gpu=True
    )

And in order to monitor this workflow we specify a hook, whose purpose is to receive events from the workflow and,
in this case, print losses and metrics on the console.

.. code-block:: python

    # create Hook to monitor training
    training_loggin_hook = LoggingHook(training_workflow.id, 'Training', None)


Running Training
================================

Finally, we train:

.. code-block:: python

    # run optimization for NUM_EPOCHS
    for i in range(NUM_EPOCHS):
        training_workflow.run()


*****************************
Contributing to Eisen
*****************************

Eisen is open to contributions by the community. Anyone can contribute to Eisen and we are constantly working to
reduce the number of guidelines that need to be followed when building it.

The truly important aspect is to keep functionality organized in many small composable blocks. The blocks should be
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