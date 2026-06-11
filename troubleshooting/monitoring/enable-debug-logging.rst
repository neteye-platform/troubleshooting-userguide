How to enable/disable debug logging
-----------------------------------

To find out more information about a particular problem, you can
follow the procedure below to enable debugging, then re-enact the
problem in the NetEye GUI, disable logging, and finally view the log
file.  Take care when you do this as debug log files can quickly
become very large.

::

   icinga2-master feature enable debuglog
   systemctl restart icinga2-master
   < - - Debug your system here - - >
   icinga2-master feature disable debuglog
   systemctl restart icinga2-master

You can find the resulting log here::

  /neteye/shared/icinga2/data/log/icinga2/debug.log</code>
