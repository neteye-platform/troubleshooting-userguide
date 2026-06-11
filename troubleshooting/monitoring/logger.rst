.. _tornado-common-logger:

Activate Debug Logging for Tornado
----------------------------------

The Tornado logger can be configured in the file :file:`/neteye/shared/tornado/conf/tornado.toml`
under the logger section. This configuration is based on three entries:

-  **level**: A list of comma separated logger verbosity levels. Valid
   values for a level are: *trace*, *debug*, *info*, *warn*, and
   *error*. If only one level is provided, this is used as global logger
   level. Otherwise, a list of per package levels can be used. E.g.:

   -  ``level=info``: the global logger level is set to *info*
   -  ``level=warn,tornado=debug``: the global logger level is set to
      *warn*, the tornado package logger level is set to *debug*

-  **stdout-output**: A boolean value that determines whether the Logger
   should print to standard output. Valid values are *true* and *false*.

-  **file-output-path**: An optional string that defines a file path in
   the file system. If provided, the Logger will append any output to
   that file.

Tornado writes all the logs of the daemon to the system journal. To inspect
them you can run:

.. code-block::

    neteye# journalctl -u tornado.service -f
