.. _ts-restart-services:

Restarting Stopped Services
---------------------------

This section helps you in solving issues related to the correct
starting of the services running on NetEye.

.. _ts-restart-services-single:

Single Node Installations
~~~~~~~~~~~~~~~~~~~~~~~~~

If you use the :command:`neteye status` command and see in the output
some service marked as ``DOWN``, when in fact it should be ``UP`` and
running, you can try to restart it. For example, take this excerpt
from the output of :command:`neteye status`.


.. container:: codeblock

   .. sourcecode::

      UP   [0] tornado.service
      DOWN [3] tornado_email_collector.service
      DOWN [0] tornado_icinga2_collector.service
      DOWN [3] tornado_nats_json_collector.service
      DOWN [3] tornado_webhook_collector.service

As an example, suppose you just installed :ref:`tornado`. In this case, the relevant information shows
Tornado as running (green ``UP``), but the collectors as stopped (red
``DOWN``), therefore you will need to start all the collector you
need. You can do by running either these four commands::

    # systemctl start tornado_email_collector.service
    # systemctl start tornado_icinga2_collector.service
    # systemctl start tornado_nats_json_collector.service
    # systemctl start tornado_webhook_collector.service

After a while, depending on how many services are restarting, run again::

    # neteye status

Alternatively, you can check the status (and restart if necessary) of
one service only, for example if you only need the email collector::

    # systemctl status tornado_email_collector.service

The output should include the line ``Active: active (running)``:

.. container:: codeblock

   .. code::

      ● tornado_email_collector.service - Tornado Email Collector - Data Collector for procmail
         Loaded: loaded (/usr/lib/systemd/system/tornado_email_collector.service; disabled; vendor preset: disabled)
        Drop-In: /etc/systemd/system/tornado_email_collector.service.d
                 └─neteye.conf
         Active: active (running) since Mon 2021-07-26 15:59:49 CEST; 15s ago
       Main PID: 24389 (tornado_email_c)
         CGroup: /docker/fe7ce88742f2ec8405c470064b414e8c73b384c2f5292605109ddfa7cf6b5373/system.slice/tornado_email_collector.service

      Jul 26 15:59:49 fe7ce88742f2 systemd[1]: Started Tornado Email Collector - Data Collector for procmail.
      Jul 26 15:59:49 fe7ce88742f2 tornado_email_collector[24389]: Jul 26 15:59:49.313  INFO tornado_email_collector: Email collector started
      Jul 26 15:59:49 fe7ce88742f2 tornado_email_collector[24389]: Jul 26 15:59:49.313  INFO tornado_email_collector: Connect to Tornado through NATS

If still the Tornado email collector shows up as ``Active: inactive
(dead)``, then you check the log entries that appear as the output of
the above command and, should they still be useless, check the full
log with the :command:`journalctl -u tornado_email_collector.service`
command.

.. hint:: If the service is stopped, the `-n 50` option (show the last
   50 lines) can prove useful, while to follow the log in real time,
   add the `-f` option to the command.

.. _ts-restart-services-cluster:

Cluster Installations
~~~~~~~~~~~~~~~~~~~~~

On a NetEye Cluster, resources (i.e., services running on a cluster)
should automatically restart when something goes wrong and they are
terminated. In case this does not happen, you need to check the
cluster to find where the resource was started the last time and then
try to manually restart it or inspect its configuration.

On cluster, the :command:`neteye status`, :command:`neteye start`, and
:command:`neteye stop` commands are not available, hence you need to
rely on :command:`pcs`, that you can run on any node of the cluster::

       # pcs status

.. container:: codeblock

   .. sourcecode::

      Cluster name: NetEye
      Stack: corosync
      Current DC: neteye01.local (version 1.1.23-1.el7_9.1-9acf116022) - partition with quorum
      Last updated: Wed Jul 28 09:47:52 2021
      Last change: Tue Jul 27 15:04:36 2021 by root via cibadmin on neteye02.local
      2 nodes configured
      74 resource instances configured
      Online: [ neteye01.local neteye02.local ]
      Full list of resources:
       cluster_ip    (ocf::heartbeat:IPaddr2):    Started neteye02.local
       Resource Group: tornado_rsyslog_collector_group
           tornado_rsyslog_collector_drbd_fs    (ocf::heartbeat:Filesystem):    Started neteye02.local
       Resource Group: tornado_group
           tornado_drbd_fs    (ocf::heartbeat:Filesystem):    Started neteye02.local
           tornado_virt_ip    (ocf::heartbeat:IPaddr2):    Started neteye02.local
           tornado    (systemd:tornado):    Started neteye02.local
       Resource Group: tornado_email_collector_group
           tornado_email_collector_drbd_fs    (ocf::heartbeat:Filesystem):    Started neteye02.local
           tornado_email_collector    (systemd:tornado_email_collector):      FAILED neteye02.local
       Resource Group: tornado_icinga2_collector_group
           tornado_icinga2_collector_drbd_fs    (ocf::heartbeat:Filesystem):    Started neteye01.local
           tornado_icinga2_collector    (systemd:tornado_icinga2_collector):    Started neteye01.local
       Resource Group: tornado_webhook_collector_group
           tornado_webhook_collector_drbd_fs    (ocf::heartbeat:Filesystem):    Started neteye02.local
           tornado_webhook_collector    (systemd:tornado_webhook_collector):    Started neteye02.local

      Failed Resource Actions:
      * tornado_email_collector_monitor_30000 on neteye02.local 'not running' (7): call=414, status=complete, exitreason='',
          last-rc-change='Wed Jul 28 09:57:21 2021', queued=0ms, exec=0ms

      Daemon Status:
       corosync: active/enabled
       pacemaker: active/enabled
       pcsd: active/enabled

In this snippet shows only tornado-related resources are shown, but
the actual output of the :command:`pcs status` command can be much
longer. At the bottom we see that `tornado_email_collector` is in a
failed state (this is shown also by the
:phone:`tornado_email_collector (systemd:tornado_email_collector):
FAILED neteye02.local` line.

Usually on a cluster a failed resource is restarted automatically, so
you can simply check again if it started by issuing again
:command:`pcs status`: If this does not happen, the resource must be
started manually by issuing these two commands.

.. code:: bash

   # pcs resource cleanup tornado_email_collector
   # pcs resource enable tornado_email_collector

Here, `cleanup` resets the status of the resource, while `enable`
allows the cluster to start the resource.

If you only need or want to restart a running resource, use

.. code:: bash

   # pcs resource restart tornado_email_collector

In case the resource still does not start and remains in a failed
state, it will be necessary to inspect the log. Most of the logs can
be checked using the same  :command:`journalctl` command shown in
previous section, provided you are on the node where the resource
failed.

.. sourcecode::
   :linenos:

   Failed Resource Actions:
   * tornado_email_collector_monitor_30000 on neteye02.local 'not running' (7): call=414, status=complete, exitreason='',
       last-rc-change='Wed Jul 28 09:57:21 2021', queued=0ms, exec=0ms

In our case, line 2 shows that `tornado_email_collector` was launched
on  **neteye02.local**, so make sure to log in to that node before
checking the log.

.. code:: bash

   # ssh neteye02.local
   # journalctl -u tornado_email_collector

Other resources may have dedicated log files saved separately on disk;
this happens for example to **logstash**, whose log is by default
saved in :file:`/neteye/shared/logstash/log/logstash-plain.log`.
