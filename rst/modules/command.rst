.. _command:


command
```````

The command module takes the command name followed by a list of
arguments, space delimited.

+--------------------+----------+---------+----------------------------------------------------------------------------+
| parameter          | required | default | comments                                                                   |
+====================+==========+=========+============================================================================+
| (free form)        | N/A      | N/A     | the command module takes a free form command to run                        |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| creates            | no       |         | a filename, when it already exists, this step will NOT be run              |
+--------------------+----------+---------+----------------------------------------------------------------------------+
| chdir              | no       |         | cd into this directory before running the command (0.6 and later)          |
+--------------------+----------+---------+----------------------------------------------------------------------------+

The given command will be executed on all selected nodes.  It will not
be processed through the shell, so variables like "$HOME" and
operations like "<", ">", "|", and "&" will not work.  As such, all
paths to commands must be fully qualified.

.. note::
   If you want to run a command through the shell (say you are using
   '<', '>', '|', etc), you actually want the 'shell' module instead.
   The 'command' module is much more secure as it's not affected by
   the user's environment.

Example action from Ansible :doc:`playbooks`::

    command /sbin/shutdown -t now

creates and chdir can be specified after the command.  For instance, if you only want to run a 
command if a certain file does not exist, you can do the following::

    command /usr/bin/make_database.sh arg1 arg2 creates=/path/to/database

The ``creates=`` and ``chdir`` options will not be passed to the actual executable.

**Background Processes and init.d scripts**

The ``command`` module may be used to launch ``init.d`` service scripts under exigent circumstances:

- running the script via ``service`` causes the daemon to die upon ansible task completion. 
  E.g. ``service name=my-init-script state=restarted``
- running the script normally via ``command`` causes the daemon to die upon ansible task completion. 
  E.g. ``command /etc/init.d/my-init-script start``

These cases indicate that the init.d script (or service) did not daemonize correctly.

One (horrendously hackish) solution is to use ``nohup`` to get around the incorrect daemonization, as follows::

    command /usr/bin/nohup /sbin/service/badly-written-server start
    
The "correct" solution is to fix the init.d script's daemonization.  A common problem is the 
init.d script launching a backgrounded process using &. This backgrounded process will be HUP'ed
when the calling shell (i.e., ansible's SSH session) terminates. The backgrounding process 
should use ``start-stop-daemon`` (on Debian-based systems, ``/sbin/start-stop-daemon``) 
or ``daemon`` (on RedHat/Fedora-based systems, in ``/etc/rc.d/init.d/functions``).

Also see `service`_ module's documentation.

