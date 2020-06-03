Eisen offers deployment capabilities by leveraging TorchServing as implemented in PyTorch 1.5.0 and newer releases.
In this way, it is possible to use models trained with Eisen as prediction services through a simple HTTP interface
that can be used directly by sending requests using any library or by leveraging the functionality offered by
the Client included in Eisen-Deploy.

Eisen-Deploy is included in the distribution of eisen and can therefore be obtained by executing

.. code-block:: console

    pip install eisen

Otherwise, it is possible to obtain Eisen-Deploy by installing only the `eisen_deploy` package

.. code-block:: console

    pip install eisen_deploy

Using Eisen-Deploy can be achieved by importing the necessary modules directly in your code.

*********************
Packaging
*********************

Eisen-Deploy implements model serving via TorchServing. Models need to be packaged in a MAR archive before being
able to be used for serving. This is achieved via packaging.

What packaging does is to create a compressed tar archive that is portable and can be deployed using TorchServing.
This functionality is documented below.

.. automodule:: eisen_deploy.packaging
    :members:
    :private-members:
    :special-members:

.. autoclass:: EisenServingMAR
   :members: __init__, pack

Handlers
=====================

The reason why we are able to ingest data that follows the specifications of Eisen, as well as use all the convenient
interfaces and design strategies of Eisen during serving, is that we have implemented a Handler class that processes
queries to the server.

This is not something users need to worry about, in fact this documentation is being included here just for development
purposes. Handlers are included automatically in the MAR and should work transparently.

.. automodule:: eisen_deploy.serving
    :members:
    :private-members:
    :special-members:

.. autoclass:: EisenServingHandler
   :members: __init__, initialize, get_metadata, pre_process, inference, post_process, handle


*********************
Server
*********************

In order to serve packaged model (MAR archives) over a HTTP interface, Eisen leverages TorchServing. We do not
implement our own server, but leverage what's being developed by PyTorch (starting from version 1.5.0).

As a compact demonstration of how to use TorchServing to instantiate a server, we include the following snippets in
this doc page.

Start serving via:

.. code-block:: console

    torchserve --start --ncs --model-store model_zoo --models model.mar


Stop serving via:

.. code-block:: console

    torchserve --stop

Note that you will need a model MAR packaged via the `EisenServingMAR` object to be able to perform inference
as explained in this doc page.


*********************
Clients
*********************

Client functionality is currently provided in Python. Of course the HTTP prediction endpoints obtained
through TorchServing can be also used by making request via a library such as requests in Python and axios in
Javascript (and all the other). Curl requests via terminal are also possible.

The Python client implemented here is documented below.


.. automodule:: eisen_deploy.client
    :members:
    :private-members:
    :special-members:

.. autoclass:: EisenServingClient
   :members: __init__, get_metadata, predict
