*********************
General Information
*********************

Eisen offers a Command Line Interface (CLI) which allows model training, validation, testing and serving.
The CLI leverages configuration files in JSON format to build complex workflows to accomplish these tasks.
A JSON file can be built using Eisen's Domain Specific Language (DSL), or be obtained through Eisen Builder utility
that can be reached at http://builder.eisen.ai

Due to the fact that Eisen's DSL is not excessively complicated, and due to the fact that Eisen Builder can
produce configuration files for arbitrary complex workflows, the DSL documentation is still pending. This will be
addressed in a future documentation update.

Eisen-CLI is included in the distribution of eisen and can therefore be obtained by executing

.. code-block:: console

    $ pip install eisen

Otherwise, it is possible to obtain Eisen-CLI by installing only the `eisen-cli` package

.. code-block:: console

    $ pip install eisen-deploy

Using Eisen-Deploy can be achieved by importing the necessary modules directly in your code.

*********************
CLI
*********************

Command line utilities for training, validation, testing and deployment are included in Eisen CLI. The
interface and purpose of each implementation is detailed here:

.. automodule:: eisen_cli
    :members: train, validate, test, serve


*********************
Intrinsics
*********************

The CLI exposes functionality to the user by implementing argument parsing and and interface. Workflows
are then executed by specific functions that parse configuration files and instantiate objects according to needs.

These functions are detailed here:

.. automodule:: eisen_cli.train
    :members: eisen_training