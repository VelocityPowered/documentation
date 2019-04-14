Commands
========

Velocity includes a few commands in the core of the proxy by default.
You can gain a richer set of commands by adding plugins.

The /velocity command
---------------------

The ``/velocity`` command contains a number of commands to help manage
the proxy.

``/velocity plugins``
^^^^^^^^^^^^^^^^^^^^^

If the user has the ``velocity.command.plugins`` permission, they can
view all the plugins currently active on the proxy.

``/velocity version``
^^^^^^^^^^^^^^^^^^^^^

Displays the Velocity proxy version.

``/velocity reload``
^^^^^^^^^^^^^^^^^^^^^

If the user has the ``velocity.command.reload`` permission, the proxy
will read and reconfigure itself from the ``velocity.toml`` on disk. If
there are problems with parsing the file, no changes will be applied.

The /server command
-------------------

If the user has the ``velocity.command.server`` permission (by default,
this is granted to all users), players can use this command to view and
change servers.

Executing just ``/server`` will send the user the name of the server they
are currently on, along with options to move to other servers configured
on the proxy.

If a server name is specified, Velocity will attempt to connect to the
server.

The /shutdown command
---------------------

When executed from the console, this will gracefully shut down the Velocity
proxy. All players will be disconnected from the proxy and plugins will have
a chance to finish up before the proxy shuts down.
