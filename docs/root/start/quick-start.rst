.. _start_quick_start:


Quick start
===========

The following instructions walk through starting Envoy as a system daemon or using
the Envoy Docker image.

Check your Envoy version
------------------------

Once you have :ref:`installed Envoy <install>`, you can check the version information as follows:

.. tabs::

   .. tab:: System

      .. code-block:: console

	 $ envoy --version

   .. tab:: Docker

      .. substitution-code-block:: console

	 $ docker run --rm envoyproxy/|envoy_docker_image| --version


View the Envoy command line options
-----------------------------------

You can view the Envoy :ref:`command line options <operations_cli>` with the ``--help``
flag:

.. tabs::

   .. tab:: System

      .. code-block:: console

	 $ envoy --help

   .. tab:: Docker

      .. substitution-code-block:: console

	 $ docker run --rm envoyproxy/|envoy_docker_image| --help


Run Envoy with the demo configuration
-------------------------------------

The ``-c`` or ``--config-path`` flag tells Envoy the path to its initial configuration.

.. tabs::

   .. tab:: System

      To start Envoy as a system daemon :download:`download the demo configuration <_include/envoy-demo.yaml>`, and start
      as follows:

      .. code-block:: console

	 $ envoy -c envoy-demo.yaml

   .. tab:: Docker

      You can start the Envoy Docker image without specifying a configuration file, and
      it will use the demo config by default.

      .. substitution-code-block:: console

	 $ docker run --rm -d -p 9901:9901 -p 10000:10000 envoyproxy/|envoy_docker_image|

      To specify a custom configuration you can mount the config into the container, and specify the path with ``-c``.

      .. substitution-code-block:: console

	 $ docker run --rm -d -v envoy-custom.yaml:/envoy-custom.yaml -p 9901:9901 -p 10000:10000 envoyproxy/|envoy_docker_image| -c /envoy-custom.yaml

Check Envoy is proxying on http://localhost:10000

.. code-block:: console

   $ curl -v localhost:10000

The Envoy admin endpoint should also be available at http://localhost:9901

.. code-block:: console

   $ curl -v localhost:9901


Override the default configuration by merging a config file
-----------------------------------------------------------

You can provide a configuration override file using ``--config-yaml`` which will merge with the main
configuration.

Save the following snippet to ``envoy-override.yaml``:

.. code-block:: yaml

   listeners:
     - name: listener_0
       address:
         socket_address:
           port_value: 20000

Next, start the Envoy server using the override configuration.

.. tabs::

   .. tab:: System

      .. code-block:: console

	 $ envoy -c envoy-demo.yaml --config-yaml envoy-override.yaml

   .. tab:: Docker

      .. substitution-code-block:: console

	 $ docker run --rm -d -v envoy-override.yaml:/envoy-override.yaml -p 20000:20000 envoyproxy/|envoy_docker_image| --config-yaml /envoy-override.yaml

Envoy should now be proxying on http://localhost:20000

.. code-block:: console

   $ curl -v localhost:20000

The Envoy admin endpoint should also be available at http://localhost:9901

.. code-block:: console

   $ curl -v localhost:9901


Static configuration
--------------------

To start Envoy with static configuration, you will need to specify ``listeners`` and ``clusters``
as ``static_resources``.

You can also add an ``admin`` section if you wish to monitor Envoy or retrieve stats.

The following sections walk through the static configuration provided in the :download:`demo configuration file <_include/envoy-demo.yaml>`.

Configuration: static_resources
*******************************

The :ref:`static_resources <envoy_v3_api_field_config.bootstrap.v3.Bootstrap.static_resources>` contains everything that is configured statically when Envoy starts,
as opposed to the means of configuring resources dynamically when Envoy is running.

.. literalinclude:: _include/envoy-demo.yaml
    :language: yaml
    :linenos:
    :lines: 1-3
    :emphasize-lines: 1

Configuration: listeners
************************

The specification of the :ref:`listeners <envoy_v3_api_file_envoy/config/listener/v3/listener.proto>`.

.. literalinclude:: _include/envoy-demo.yaml
    :language: yaml
    :linenos:
    :lines: 1-25
    :emphasize-lines: 3-23


Configuration: clusters
***********************

The specification of the :ref:`clusters <envoy_v3_api_file_envoy/service/cluster/v3/cds.proto>`.

.. literalinclude:: _include/envoy-demo.yaml
    :language: yaml
    :lineno-start: 22
    :lines: 22-47
    :emphasize-lines: 4-24

Configuration: admin
********************

The :ref:`admin message <envoy_v3_api_msg_config.bootstrap.v3.Admin>` is required to configure
the administration server.

The ``address`` key specifies the listening :ref:`address <envoy_v3_api_file_envoy/config/core/v3/address.proto>`
which in the demo configuration is ``0.0.0.0:9901``.

.. literalinclude:: _include/envoy-demo.yaml
    :language: yaml
    :lineno-start: 45
    :lines: 45-50
    :emphasize-lines: 3-6

.. warning::

   You may wish to restrict the network address the admin server listens to in your own deployment.


Dynamic configuration
---------------------

See the :ref:`configuration overview <config_overview>` for further information on configuring Envoy with static and dynamic configuration.


Next steps
----------

- Learn more about :ref:`using the Envoy Docker image <start_docker>`
- Try out demo configurations in the :ref:`sandboxes <start_sandboxes>`
- Check out the :ref:`configuration generator <start_tools_configuration_generator>` and other
  :ref:`Envoy tools <start_sandboxes>`
