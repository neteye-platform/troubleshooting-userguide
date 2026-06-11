Director does not deploy when services assigned to a host have the same name
----------------------------------------------------------------------------

After adding a new service to a host, whether via an added or applied
Service Set, Director does not deploy the new configuration,
displaying the following error in the Deployments panel::

  Error: An object with type 'Service' and name 'host.example.com!Service Name' already exists in servicesets.conf

The root cause is that Icinga requires that the **names** of all
services included in any service set on a given host be distinct (the
services themselves can be duplicated as long as they are given unique
names).  You can check this by going to the host indicated in the
error message, clicking on the Services tab, and looking for identical
names of services in the listed Service Sets.

This problem typically arises when you select the "Add Service" action
on the Services tab of a particular Service Set, or use an "Assign
where" rule, and then choose a service template from the Imports
dropdown.  Director automatically prefills the **Name** field with the
name of the template you selected in the **Imports** field.  You can
avoid this problem by giving a unique name to the service in the
**Name** field.
