*********************
Datasets
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

.. autoclass:: CAMUS
   :members: __init__

.. autoclass:: RSNAIntracranialHemorrhageDetection
   :members: __init__

.. autoclass:: RSNABoneAgeChallenge
   :members: __init__

.. autoclass:: MedSegCovid19
   :members: __init__

.. autoclass:: UCSDCovid19
   :members: __init__

.. autoclass:: PANDA
   :members: __init__

.. autoclass:: KaggleCovid19
   :members: __init__

.. autoclass:: ABCsDataset
   :members: __init__

.. autoclass:: EMIDEC
   :members: __init__

*********************
I/O
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

.. autoclass:: LoadITKFromFilename
   :members: __init__

.. autoclass:: LoadDICOMFromFilename
   :members: __init__

.. autoclass:: LoadPILImageFromFilename
   :members: __init__

.. autoclass:: WriteNiftiToFile
   :members: __init__


*********************
Transforms
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

.. autoclass:: ResampleITKVolumes
   :members: __init__

.. autoclass:: NiftiToNumpy
   :members: __init__

.. autoclass:: ITKToNumpy
   :members: __init__

.. autoclass:: PilToNumpy
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

.. autoclass:: RepeatTensor
   :members: __init__

.. autoclass:: NumpyToNifti
   :members: __init__


*********************
Models
*********************

In order to favor reproducibility of deep learning approaches and easy benchmarking, as well as
providing "starter-kit" tools to users approaching a certain problem for the first time,
we include several well-known neural network architecture within Eisen. This is similar to the approach
taken by torchvision which ships network architectures for classification, segmentation and beyond within the package.

Models can be used in any custom code, with or without the rest of the functionality provided by Eisen. In this
sense, they are standard `torch.nn.Module` objects.

Models can be used within Eisen workflows (see below) after wrapping them
via `EisenModuleWrapper` which is available in the `eisen.utils` submodule. This also allows third party models, such
as those available in torchvision, to be used within Eisen.


Segmentation Models
=========================

Several models for segmentation are already included in Eisen. These approaches have been successfully used in
several academic works. Refer to the related publications to obtain more information.

.. automodule:: eisen.models.segmentation
    :members:
    :private-members:
    :special-members:

.. autoclass:: UNet3D
   :members: __init__, forward

.. autoclass:: UNet
   :members: __init__, forward

.. autoclass:: VNet
   :members: __init__, forward

.. autoclass:: ObeliskMIDL
   :members: __init__, forward

.. autoclass:: HighRes2DNet
   :members: __init__, forward

.. autoclass:: HighRes3DNet
   :members: __init__, forward


*********************
Ops
*********************

Eisen includes various operations that are useful when developing deep learning models.
The operations are always implemented in PyTorch and derived from the class
`torch.nn.Module` as suggested by the PyTorch documentation itself.
Eisen contains implementations of layers, metrics and losses. Losses and metrics
implementation include methods such as the Dice loss, which find useful
application especially in tasks belonging to the medical domain.

Losses
=========================

When optimizing neural network parameters during training, it is crucial to specify a suitable loss
that pushes the network towards solving the problem at hand. Eisen includes losses that can be optimized during
training and computed during validation.

.. automodule:: eisen.ops.losses
    :members:
    :private-members:
    :special-members:

.. autoclass:: DiceLoss
   :members: __init__, forward


Metrics
=========================

Benchmarking, testing and validating models often requires computing metrics that can give an estimate of the
performance of the network on the problem at hand. Eisen includes metrics modules that can be computed during
training, validation and testing.

.. automodule:: eisen.ops.metrics
    :members:
    :private-members:
    :special-members:

.. autoclass:: DiceMetric
   :members: __init__, forward


*********************
Workflows
*********************

Workflows realize high level functionality that joins several building blocks such as losses, metrics, transforms
optimizers and models together in order to perform operations on them.

.. warning::

    Eisen-Core versions after 0.0.5 (Eisen versions after 0.1.6) and current versions installed from GitHub repository
    introduce breaking changes to workflows and wrappers.
    Model or Data parallelism need to be taken care of before passing the model to the workflow.
    This documentation illustrates the most recent way of using workflows.

Workflows possess unique IDs. It is possible to retrieve this ID as `workflow.id`, where `workflow` is a Workflow
instance.

Typical examples of workflows are Training, Testing and Validation. These basic workflows implement respectively the
training, testing and validation loops. Workflows are implemented as python objects similar to this:

.. code-block:: python

    from eisen.utils.workflows import GenericWorkflow

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

.. autoclass:: GenericWorkflow
   :members: __init__, __call__

.. autoclass:: Training
   :members: __init__, __call__, run

.. autoclass:: TrainingApexAMP
   :members: __init__, __call__, run

.. autoclass:: TrainingAMP
   :members: __init__, __call__, run

.. autoclass:: Testing
   :members: __init__, __call__, run

.. autoclass:: Validation
   :members: __init__, __call__, run


*********************
Hooks
*********************

Eisen hooks are triggered when specific events are generated by Eisen workflows. These hooks are python modules
implementing things such as logging, model serialization and summary export, which are executed at the end of an epoch
or upon detection of a model exhibiting superior performance with respect losses or metrics.

Hooks are designed to process `output_dictionaries` obtained from workflows. These output dictionaries are generated
and aggregated across an epoch during execution of the workflow. They are ultimately sent to all the hooks listening
to events originated from each specific workflow.

The supported events are:

- `eisen.EISEN_END_BATCH_EVENT`
- `eisen.EISEN_END_EPOCH_EVENT`
- `eisen.EISEN_BEST_MODEL_LOSS`
- `eisen.EISEN_BEST_MODEL_METRIC`

Hooks are only listening to events generated by workflows that they are monitoring. Workflows are identified
by their unique ID. They require minimum three parameters to be instantiated:

- `workflow_id`
- `phase`
- `artifact_dir`

It is possible to associate an arbitrary number of hooks to each workflow. They will be executed in sequence. An example
of hook instantiation is shown here:

.. code-block:: python

    from eisen.utils.logging import LoggingHook
    from eisen.utils.logging import TensorboardSummaryHook

    # let us suppose a workflow has been already instantiated

    first_hook = LoggingHook(workflow.id, 'Training', './')
    second_hook = TensorboardSummaryHook(workflow.id, 'Training', './')

The two hooks in this example, `first_hook` and `second_hook` will listen for events such as
`EISEN_END_EPOCH_EVENT` in order to execute their actions on the workflow's output_dictionary.
The typical content (keys) of this dictionary is:

- `losses` which contains a list of the losses computed during a batch or epoch
- `metrics` which contains a list of the metrics computed during a batch or epoch
- `inputs` which contains a dictionary of most recent inputs fed to the network
- `outputs` which contains a dictionary of most recent outputs produced by the network
- `model` which contains a pointer (not a deep copy) to the current model
- `epoch` which is an integer representing the current epoch number

Refer to the following documentation to learn more.

Logging hooks
=========================

These hooks are mainly used to monitor training. They are able to extract information such as losses, metrics and
examples of inputs and outputs and display them to the user.

.. automodule:: eisen.utils.logging
    :members:
    :private-members:
    :special-members:

.. autoclass:: LoggingHook
   :members: __init__

.. autoclass:: TensorboardSummaryHook
   :members: __init__


Artifacts generation hooks
=================================

These hooks are used to save artifacts as a result of a workflow. They save models snapshots in different formats.
It is necessary to note that the snapshot are saved as *unwrapped* `torch.nn.Module` (they do not belong to
EisenModuleWrapper class).

.. automodule:: eisen.utils.artifacts
    :members:
    :private-members:
    :special-members:

.. autoclass:: SaveTorchModelHook
   :members: __init__

.. autoclass:: SaveONNXModelHook
   :members: __init__


*********************
Artifacts
*********************

Artifacts can be generated without using hooks. Objects of type `torch.nn.Module` can be serialized in different
formats using functionality provided by Eisen. Naturally, they behave just like any other torch.nn.Module therefore
can be saved also using other methods. This is true for any model used into Eisen, even when no workflow has been
used for training, testing and validation.

It is suggested *NOT* to use wrapped modules during serialization. That is, serializing a `EisenModuleWrapper` object,
even if such object is derived from `torch.nn.Module` can result in issues depending on the chosen type of
serialization.

Refer to the following documentation to learn more.

.. automodule:: eisen.utils.artifacts
    :members:
    :private-members:
    :special-members:

.. autoclass:: SaveTorchModel
   :members: __init__, __call__

.. autoclass:: SaveONNXModel
   :members: __init__, __call__


*********************
Wrappers
*********************

Many packages in the PyTorch echosystem such as torchvision, are not fully compatible with Eisen. Eisen makes
heavy use of dictionaries throught most of the objects in the module `eisen.utils` and beyond.

Dataset entries are represented as dictionaries, batches are also dictionaries, input and output of modules (models,
losses, metrics) are also expected to be dictionaries.

This architecture is unfortunately not universally adopted, and since it is the key of the flexibility of Eisen,
adaptors have been developed in order to make functionality inherited from other packages fully compatible with Eisen
with no significant impact on performance.

Our wrappers are adaptors that perform simple translation of input and outputs variables from and to the specific format
expected by Eisen. We include below the documentation of our wrappers with usage examples.

.. warning::

    Eisen-Core versions after 0.0.5 (Eisen versions after 0.1.6) and current versions installed from GitHub repository
    introduce breaking changes to workflows and wrappers.
    Wrappers require an instance of a Module, Transform or Dataset rather than a Module, Transform or Dataset type.
    This documentation illustrates the most recent way of using wrappers.

.. automodule:: eisen.utils

.. autoclass:: EisenModuleWrapper
   :members: __init__

.. autoclass:: EisenTransformWrapper
   :members: __init__

.. autoclass:: EisenDatasetWrapper
   :members: __init__


*********************
Other utilities
*********************

Eisen contains other utility functions and objects that can be be used to further improve functionality and often
independently from Eisen itself. Therefore we report the documentation for these modules.

.. automodule:: eisen.utils

.. autoclass:: PipelineExecutionStreamer
   :members: __init__

.. autoclass:: ModelParallel
   :members: __init__
