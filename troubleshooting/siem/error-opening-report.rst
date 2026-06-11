Reporting: Error when opening a report
--------------------------------------

After having created or modified a report definition with a filter
expression, the Reporting module appears to save the new definition
properly.  But then while attempting to reopen that report definition,
the Reporting module crashes with the following error::

  Cannot apply the filter &lt;filter-expr&gt;. You can only use the following columns: instance_name, host_name, hostgroup_name, service_description, servicegroup_name, _(host|service)_&lt;customvar-name&gt;
  Invalid filter column provided: host

This error occurs because the set of variables allowed in filtering
expressions is restricted.  You may only use one of the variables
listed in the :ref:`monitoring permissions reference guide
<monitor-visual-permissions>`.

When you see the above error, you should click on the |edit| ``Modify``
action at the top of the panel and fix the filtering expression.  You
can then update the report definition to use the correct variable name
and continue to use the report as normal.
