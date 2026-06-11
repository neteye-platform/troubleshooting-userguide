Sync Rule fails when trying to recreate Icinga object
-----------------------------------------------------

When creating a sync rule for object import, the **key column name**
from the parent Import Source is used as the ID during the matching
phase.  The key column name cannot be directly modified by adding a
custom expression.  Should this occur, you will see the following
error::

  This Sync Rule failed when last checked at 2019-01-15 09:35:08: Exception while syncing Icinga\Module\Director\Objects\IcingaHost linuxhost7.location: Trying to recreate icinga_host

To restore operation, you can either:

* Remove the modification to the sync property so the index is not
  customized
* Change the **key column name** to identify imported objects with a
  different unique key
* Use instead a new field created by an import source property
  modifier such as **Combine multiple properties**
