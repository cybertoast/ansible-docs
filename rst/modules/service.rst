.. _service:

service
```````

Controls services on remote machines.

+--------------------+----------+---------+----------------------------------------------------------------------------+
| parameter          | required | default | comments                                                                   |
+====================+==========+=========+============================================================================+
| name               | yes      |         | name of the service                                                        |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| state              | no       | started | 'started', 'stopped', 'reloaded', or 'restarted'.  Started/stopped are     |
|                    |          |         | idempotent actions that will not run commands unless neccessary.           |
|                    |          |         | 'restarted' will always bounce the service, 'reloaded' will always reload. |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| pattern            | no       |         | (new in 0.7) if the service does not respond to the status command,        |
|                    |          |         | name a substring to look for as would be found in the output of the 'ps'   |
|                    |          |         | command as a stand-in for a status result.  If the string is found, the    |
|                    |          |         | service will be assumed to be running.                                     |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| enabled            | no       |         | Whether the service should start on boot.  Either 'yes' or 'no'.           |
+--------------------+----------+---------+----------------------------------------------------------------------------+

Example actions from Ansible :doc:`playbooks`::

    service name=httpd state=started
    service name=httpd state=stopped
    service name=httpd state=restarted
    service name=httpd state=reloaded
    service name=foo pattern=/usr/bin/foo state=started


**(Broken) Daemons**

In some cases the init.d/service may background a process which dies as soon 
as the ansible task completes (i.e., as soon as the ansible shell terminates).
This is indication that daemonization did not happen correctly. A workaround 
is to use the ``command`` module with ``nohup``. 

Also see the `command`_ module's documentation for more details.

Obviously, the correct solution is to fix the broken daemonization process!
