*********************
Eisen
*********************
.. contents:: Table of Contents


*********************
Eisen.datasets
*********************

Datasets are used in Eisen to bring data into the training/validation/testing or serving pipeline. They are core
functionality to Eisen together with transforms, I/O operations, models and other constructs.

Eisen Datasets are very similar to those commonly used in pytorch. In this sense they implement a `__init__`, `__len__`
and `__getitem__` methods.

Users need only to instantiate these modules using the appropriate set of parameters and the rest will be handled by
Eisen. An example on how to get started on Datasets can be found in the Eisen colab example and is summarized here.

.. code-block:: python

    from eisen.datasets import MSDData
    from torch.utils.data import DataLoaderset

    # ... define transform chain ...

    dataset = MSDDataset(
        PATH_DATA,
        NAME_MSD_JSON,
        'training',
        transform=transform
    )

    # create data loader, this functionality is pure pytorch
    data_loader = DataLoader(
        dataset,
        batch_size=BATCH_SIZE,
        shuffle=True,
        num_workers=4
    )

Upon instantiation it is necessary to create and define the transforms that will manipulate the dataset. Futher
documentation about the transforms currently implemented in Eisen, as well as general directions on how to implement
new ones are included below.

It is important to know that data in Eisen is always represented as a list of dictionaries. Each entry of the list is
a dictionary containing data belonging to the same datapoint instance.

.. code-block:: python

    my_example_dataset = [
        {"image": "/path/to/image1.jpg", "label": "/path/to/label1.jpg"},
        {"image": "/path/to/image2.jpg", "label": "/path/to/label2.jpg"},
        {"image": "/path/to/image3.jpg", "label": "/path/to/label3.jpg"},
    ]

The example above conveys the general form that a dataset assumes inside Eisen. This form has to be taken into account
when implementing your own Datasets. Once the data is organized in this way it can be processed by Transforms.
The transforms are fed individual entries of the list and act on one or multiple fields of the resulting dictionary.

.. automodule:: eisen.datasets
    :members:
    :private-members:
    :special-members:

.. autoclass:: JsonDataset
   :members: __init__

.. autoclass:: MSDDataset
   :members: __init__

.. autoclass:: PatchCamelyon
   :members: __init__


*********************
Eisen.io
*********************

Eisen I/O functionality is contained in this module. I/O functionality is implemented by transforms. That is, this
functionality behaves just like any other eisen.transform module. The only difference is that they operate on disk.
Another reason of this distinction is that we decided to follow the package structure of torchvision, which has an
torchvision.io sub-package.

An example of how I/O functionality can be used to load Nifti data is contained in the Eisen example Colab notebook
and it is also shown in compact form here:

.. code-block:: python

    from eisen.datasets import MSDData
    from eisen.io import LoadNiftyFromFilename

    my_reader = LoadNiftyFromFilename(['image', 'label'], PATH_DATA)

    dataset = MSDDataset(
        PATH_DATA,
        NAME_MSD_JSON,
        'training',
        transform=my_reader
    )

Just like regular transforms, the I/O transforms can be called on data. The data needs to be a Python dictionary
and the call can be done in this way:

.. code-block:: python

    from eisen.io import LoadNiftyFromFilename

    # Assuming my_data_dictionary to be a dictionary containing data

    my_reader = LoadNiftyFromFilename(['image', 'label'], PATH_DATA)

    my_data_dictionary = my_reader(my_data_dictionary)

.. automodule:: eisen.io
    :members:
    :private-members:
    :special-members:

.. autoclass:: LoadNiftyFromFilename
   :members: __init__


*********************
Eisen.transforms
*********************

.. automodule:: eisen.transforms
    :members:
    :private-members:
    :special-members:

Transforms are used to manipulate data in Eisen. Transforms are Python Objects that can be instantiated
through the `__init__` method and implement a `__call__` method. Transforms can be stacked
and composed together using `torchvision.transforms.Compose`.

The reason of the flexibility and composibility of Eisen Transforms - and PyTorch transforms in general -
is that their `__call__` method implements a standard interface. In the case of Eisen it is:

.. code-block:: python

    def __call__(self, data):
        # Do something
        return data

In this code snippet, data is a Python dictionary that contains the data to be processed by the transforms.
Each key in this dictionary is a different data field. For example, in the case of an imaging dataset,
keys could be `['image', 'label']`. The transform will operate on one or more keys of the dictionary and
return a new dictionary with data updated as a result of the transform.

A transform is therefore implemented as a Python object as demonstrated here:

.. code-block:: python

    class Transform:
        def __init__(self):
            # Do something
            pass

        def __call__(self, data):
            # Do something
            return data

A more concrete example can be seen below. In this example we instantiate a ResampleNiftiVolumes transform
which operated on `'images'` and `'labels'` and imposes a resolution (in millimeters) for both the Nifti images
contained in `data['images']` and `data['labels']` of `[1.0, 1.0, 1.0]` millimeters. The interpolation used here is
linear.

.. code-block:: python

    from eisen.transforms import ResampleNiftiVolumes

    resample_tform = ResampleNiftiVolumes(
        ['image', 'label'],
        [1.0, 1.0, 1.0],
        'linear'
    )

    data = resample_tform(data)

Further documentation of the functionality of the Transforms module is reported below.


Imaging Transforms
=========================

Imaging transforms are used to handle 2D and 3D imaging data. These transforms implement basic data manipulation
such as resampling, cropping, thresholding, normalizing, etc which are useful in the context of data pre-processing
for deep learning tasks.

.. automodule:: eisen.transforms.imaging

.. autoclass:: CreateConstantFlags
   :members: __init__

.. autoclass:: RenameFields
   :members: __init__

.. autoclass:: FilterFields
   :members: __init__

.. autoclass:: ResampleNiftiVolumes
   :members: __init__

.. autoclass:: NiftiToNumpy
   :members: __init__

.. autoclass:: CropCenteredSubVolumes
   :members: __init__

.. autoclass:: MapValues
   :members: __init__

.. autoclass:: ThresholdValues
   :members: __init__

.. autoclass:: AddChannelDimension
   :members: __init__

.. autoclass:: StackImagesChannelwise
   :members: __init__

.. autoclass:: CropCenteredSubVolumes
   :members: __init__

.. autoclass:: LabelMapToOneHot
   :members: __init__

.. autoclass:: FixedMeanStdNormalization
   :members: __init__


*********************
Eisen.utils.workflows
*********************

Workflows realize high level functionality that joins several building blocks such as losses, metrics, transforms
optimizers and models together in order to perform operations on them.

Typical examples of workflows are Training, Testing and Validation. These basic workflows implement respectively the
training, testing and validation loops. Workflows are implemented as python objects similar to this:

.. code-block:: python

    from eisen.workflow import GenericWorkflow

    class DemoWorkflow(GenericWorkflow):
        def __init__(
            self,
            model,
            data_loader,
            losses,
            optimizer,
            metrics
        ):

        # ...

In particular, the arguments of the `__init__` function represent building blocks that need to be used together
during the workflow.

Refer to the following documentation to learn more.

.. automodule:: eisen.utils.workflows
    :members:
    :private-members:
    :special-members:

.. autoclass:: Training
   :members: __init__

.. autoclass:: TrainingAMP
   :members: __init__

.. autoclass:: Testing
   :members: __init__

.. autoclass:: Validation
   :members: __init__


*********************
Eisen Wrappers
*********************

Many packages in the PyTorch echosystem such as torchvision, are not fully compatible with Eisen. Eisen makes
heavy use of dictionaries throught most of the objects in the module `eisen.utils` and beyond.

Dataset entries are represented as dictionaries, batches are also dictionaries, input and output of modules (models,
losses, metrics) are also expected to be dictionaries.

This architecture is unfortunately not universally adopted, and since it is the key of the flexibility of Eisen,
adaptors have been developed in order to make functionalities inherited from other packages fully compatible with Eisen
with no significant impact on performance.

Our wrappers are adaptors that perform simple translation of input and outputs variables from and to the specific format
expected by Eisen. We include below the documentation of our wrappers with usage examples.

.. automodule:: eisen.utils

.. autoclass:: EisenModuleWrapper
   :members: __init__

.. autoclass:: EisenTransformWrapper
   :members: __init__

.. autoclass:: EisenDatasetWrapper
   :members: __init__
