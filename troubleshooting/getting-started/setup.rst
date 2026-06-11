.. _ts-getting-started-setup:

Setup
=====

Authentication
--------------

.. _ts-getting-started-setup-keycloak-debug-log:

Generic Keycloak problems resolution with logs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are experiencing problems with the authentication, IDP integration, errors printed out in the web UI of Keycloak,
etc, you can check the Keycloak logs to understand what is happening.
Keycloak logs pretty much everything that is happening and can be very useful to understand what is going wrong.

The activation of the debug log can be done by setting `log-level=DEBUG` in
the `/neteye/shared/keycloak/conf/keycloak.conf` file and then restarting the service.
To restart the service run `systemctl restart keycloak` in single node installations
and `pcs resource restart keycloak` in cluster installations.


.. _ts-getting-started-setup-keycloak-duplicated-email:

Email already exists (multiple accounts with the same email)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If during the login process you receive an error message like

.. code::

   Email already exists


it means that there are multiple accounts with the same email.

The same problem can be found by analyzing the Keycloak logs by finding a message like

.. code::

   Failed during import user from LDAP: org.keycloak.models.ModelDuplicateException: Can't import user 'USER1' from LDAP because email 'user1@example.com' already exists in Keycloak. Existing user with this email is 'USER2'


There are two ways to solve this problem:

1. **Change the email of the user in the LDAP/AD server**:
   This is the best solution because it will prevent the problem from happening again.
   The email is a unique field in Keycloak, so it is not possible to have multiple accounts with the same email.
   After changing the email in the LDAP server, you can trigger a synchronization in Keycloak to update the user information.

2. **Allow duplicate emails in Keycloak**:
   This is a solution that can be used if you can't change the email in the LDAP/AD server.
   To allow duplicate emails in Keycloak, in :menuselection:`Realm settings / Login`,
   **disable** the **Login with email** option and **enable** the **Duplicate emails** option.
   This will allow multiple accounts with the same email in Keycloak, with the downside
   that it will not to be possible to login with emails anymore, but only usernames.


The migrated LDAP connection doesn't work in Keycloak
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When this problem occurs, you can check out the logs of Keycloak to understand what is happening.
To do this, you should raise the log level of the Keycloak service as described
:ref:`in the section above <ts-getting-started-setup-keycloak-debug-log>`.

This can be done by setting `log-level=DEBUG` in the `/neteye/shared/keycloak/conf/keycloak.conf` file
and then restarting the service. To restart the service run `systemctl restart keycloak` in single node installations
and `pcs resource restart keycloak` in cluster installations.

After that, you can read the logs in the service journal, opened with the command `journalctl -fu keycloak`.
In cluster installations be sure to check the logs of the node where the Keycloak service is running.
Keeping the terminal opened, navigate to the Keycloak admin console with your browser.
You can can find more details at :ref:`in this section <auth-admin-console>`.

Once logged in the Keycloak admin console, you can test the LDAP configuration.
From the left menu, navigate to :menuselection:`User federation` and click on the LDAP provider that is not working.
In the Connection and authentication settings, you can find the `Test connection` button,
that will allow you to see the logs in the terminal to understand what is happening.
If the test connection succeeds, you can also try out the `Test authentication` button.
