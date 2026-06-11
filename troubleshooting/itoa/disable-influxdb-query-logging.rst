How to disable InfluxDB query logging
-------------------------------------

Using the default options, InfluxDB is very verbose, it logs every http query it receives.
So basically every query is being logged by default.

To avoid this behavior add the ``log-enabled = false`` option in the ``[http]`` section
of InfluxDB config file ``/neteye/shared/influxdb/conf/influxdb.conf``.

.. code:: bash

  [http]

  # Determines whether HTTP request logging is enabled.
  log-enabled = false

Then restart influxdb.

* NetEye Single Instances and Satellites:

.. code:: bash

  systemctl restart influxdb

* NetEye Clusters:

.. code:: bash

  pcs resource restart influxdb
