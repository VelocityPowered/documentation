Configuring Velocity
====================

Velocity has been designed to be simple and unambigous to configure.

The configuration file
----------------------

Velocity is largely configured from the ``velocity.toml`` file. This file is
created in the directory where you started the proxy.

The configuration format
^^^^^^^^^^^^^^^^^^^^^^^^

Before we continue, it is useful to take a step back and note that Velocity uses
the `TOML <https://github.com/toml-lang/toml>`_ format for its configuration.
TOML was designed to be easy to understand, so you should not have difficulty
understanding Velocity's configuration file.

Root section
^^^^^^^^^^^^

These settings mostly cover the basic, most essential settings of the proxy.

+-----------------------------------+------------+-------------------------+---------------------------------------+
| Setting name                      | Type       | Default                 | Description                           |
+===================================+============+=========================+=======================================+
| ``bind``                          | Address    | ``0.0.0.0:25577``       | This tells the proxy to accept        |
|                                   |            |                         | connections on a specific IP.         |
|                                   |            |                         | By default, Velocity will listen      |
|                                   |            |                         | for connections on all IP addresses   |
|                                   |            |                         | on the computer on port 25577.        |
+-----------------------------------+------------+-------------------------+---------------------------------------+
| ``motd``                          | Chat       | ``&3A Velocity Server`` | This allows you to change the         |
|                                   |            |                         | message shown to players when they    |
|                                   |            |                         | add your server to their server       |
|                                   |            |                         | list. You can use legacy Minecraft    |
|                                   |            |                         | color codes or JSON chat.             |
+-----------------------------------+------------+-------------------------+---------------------------------------+
| ``show-max-players``              | Integer    | ``500``                 | This allows you to customize the      |
|                                   |            |                         | number of "maximum" players in the    |
|                                   |            |                         | player's server list. Note that       |
|                                   |            |                         | Velocity doesn't have a maximum       |
|                                   |            |                         | number of players it supports.        |
+-----------------------------------+------------+-------------------------+---------------------------------------+
| ``player-info-forwarding``        | Mode       | ``modern``              | This allows you to customize how      |
|                                   |            |                         | player information such as IPs and    |
|                                   |            |                         | UUIDs are forwarded to your server.   |
|                                   |            |                         | See the "Player info forwarding"      |
|                                   |            |                         | section for more information.         |
+-----------------------------------+------------+-------------------------+---------------------------------------+
| ``player-info-forwarding-secret`` | String     | ``5up3r53cr3t``         | This setting is used as a secret to   |
|                                   |            |                         | ensure that player info forwarded     |
|                                   |            |                         | by Velocity comes from your proxy     |
|                                   |            |                         | and not from someone pretending to    |
|                                   |            |                         | run Velocity. See the "Player info    |
|                                   |            |                         | forwarding" section for more info.    |
+-----------------------------------+------------+-------------------------+---------------------------------------+
| ``announce-forge``                | Boolean    | ``false``               | This setting determines whether or    |
|                                   |            |                         | Velocity should present itself as a   |
|                                   |            |                         | Forge/FML-compatible server. By       |
|                                   |            |                         | default, this is disabled.            |
+-----------------------------------+------------+-------------------------+---------------------------------------+

``server`` section
^^^^^^^^^^^^^^^^^^

+------------------------+------------+-------------------------+----------------------------------------+
| Setting name           | Type       | Default                 | Description                            |
+========================+============+=========================+========================================+
| A server name          | Address    | See the default         | This makes the proxy aware of a server |
|                        |            | configuration below.    | that it can connect to.                |
+------------------------+------------+-------------------------+----------------------------------------+
| ``try``                | Array      | ``["lobby"]``           | This specifies what servers (in order  |
|                        |            |                         | Velocity should try to connect to upon |
|                        |            |                         | player login and when a player is      |
|                        |            |                         | kicked from a server.                  |
+------------------------+------------+-------------------------+----------------------------------------+

``advanced`` section
^^^^^^^^^^^^^^^^^^^^

+---------------------------+------------+----------+----------------------------------------+
| Setting name              | Type       | Default  | Description                            |
+===========================+============+==========+========================================+
| ``compression-threshold`` | Integer    | ``1024`` | This is the minimum size (in bytes)    |
|                           |            |          | that a packet has to be before the     |
|                           |            |          | proxy compresses it. Minecraft uses    |
|                           |            |          | 256 bytes by default. Velocity uses a  |
|                           |            |          | higher value for efficiency.           |
+---------------------------+------------+----------+----------------------------------------+
| ``compression-level``     | Integer    | ``-1``   | This setting indicates what ``zlib``   |
|                           |            |          | compression level the proxy should use |
|                           |            |          | to compress packets. The default value |
|                           |            |          | uses the default zlib level, which is  |
|                           |            |          | dependent on the zlib version. This    |
|                           |            |          | number goes from ``0`` to ``9``, where |
|                           |            |          | ``0`` means no compression and ``9``   |
|                           |            |          | indicates maximum compression.         |
+---------------------------+------------+----------+----------------------------------------+
| ``login-ratelimit``       | Integer    | ``3000`` | This setting determines the minimum    |
|                           |            |          | amount of time (in milliseconds) that  |
|                           |            |          | must pass before a connection from the |
|                           |            |          | same IP address will be accepted by    |
|                           |            |          | the proxy. A value of ``0`` disables   |
|                           |            |          | the rate limit.                        |
+---------------------------+------------+----------+----------------------------------------+
| ``connection-timeout``    | Integer    | ``5000`` | This setting determines how long the   |
|                           |            |          | proxy will wait to connect to a server |
|                           |            |          | before timing out.                     |
+---------------------------+------------+----------+----------------------------------------+
| ``read-timeout``          | Integer    | ``30000``| This setting determines how long the   |
|                           |            |          | proxy will wait to receive data from   |
|                           |            |          | the server before timing out. If you   |
|                           |            |          | use Forge, you may need to increase    |
|                           |            |          | this setting.                          |
+---------------------------+------------+----------+----------------------------------------+
| ``proxy-protocol``        | Boolean    | ``false``| This setting determines whether or not |
|                           |            |          | Velocity should receive HAProxy PROXY  |
|                           |            |          | messages. If you don't use HAProxy,    |
|                           |            |          | leave this setting off.                |
+---------------------------+------------+----------+----------------------------------------+

``query`` section
^^^^^^^^^^^^^^^^^

+-----------------+-------------+-----------+-------------------------------------------+
| Setting name    | Type        | Default   | Description                               |
+=================+=============+===========+===========================================+
| ``enabled``     | Boolean     | ``false`` | Whether or not Velocity should reply to   |
|                 |             |           | GameSpy 4 (Minecraft query protocol)      |
|                 |             |           | requests. You can usually leave this      |
|                 |             |           | false.                                    |
+-----------------+-------------+-----------+-------------------------------------------+
| ``port``        | Number      | ``25577`` | Specifies which port that Velocity should |
|                 |             |           | listen on for GameSpy 4 (Minecraft query  |
|                 |             |           | protocol) requests.                       |
+-----------------+-------------+-----------+-------------------------------------------+
| ``map``         | String      | Velocity  | Specifies the map name to be shown to     |
|                 |             |           | clients.                                  |
+-----------------+-------------+-----------+-------------------------------------------+
| ``show-plugins``| Boolean     | False     | Whether or not Velocity plugins are       |
|                 |             |           | included in query responses.              |
+-----------------+-------------+-----------+-------------------------------------------+

The default configuration
-------------------------

Below is the default configuration file for Velocity, ``velocity.toml``.

.. code-block:: plaintext
    :caption: velocity.toml

    # What port should the proxy be bound to? By default, we'll bind to all addresses on port 25577.
    bind = "0.0.0.0:25577"

    # What should be the MOTD? Legacy color codes and JSON are accepted.
    motd = "&3A Velocity Server"

    # What should we display for the maximum number of players? (Velocity does not support a cap
    # on the number of players online.)
    show-max-players = 500

    # Should we authenticate players with Mojang? By default, this is on.
    online-mode = true

    # Should we forward IP addresses and other data to backend servers?
    # Available options:
    # - "none":   No forwarding will be done. All players will appear to be connecting from the proxy
    #             and will have offline-mode UUIDs.
    # - "legacy": Forward player IPs and UUIDs in BungeeCord-compatible fashion. Use this if you run
    #             servers using Minecraft 1.12 or lower.
    # - "modern": Forward player IPs and UUIDs as part of the login process using Velocity's native
    #             forwarding. Only applicable for Minecraft 1.13 or higher.
    player-info-forwarding = "modern"

    # If you are using modern IP forwarding, configure an unique secret here.
    player-info-forwarding-secret = "5up3r53cr3t"

    # Announce whether or not your server supports Forge/FML. If you run a modded server, we suggest turning this on.
    announce-forge = false
    
    [servers]
    # Configure your servers here.
    lobby = "127.0.0.1:30066"
    factions = "127.0.0.1:30067"
    minigames = "127.0.0.1:30068"

    # In what order we should try servers when a player logs in or is kicked from a server.
    try = [
        "lobby"
    ]

    [advanced]
    # How large a Minecraft packet has to be before we compress it. Setting this to zero will compress all packets, and
    # setting it to -1 will disable compression entirely.
    compression-threshold = 1024

    # How much compression should be done (from 0-9). The default is -1, which uses zlib's default level of 6.
    compression-level = -1

    # How fast (in miliseconds) are clients allowed to connect after the last connection? Default: 3000
    # Disable by setting to 0
    login-ratelimit = 3000

    # Specify a custom timeout for connection timeouts here. The default is five seconds.
    connection-timeout = 5000

    # Specify a read timeout for connections here. The default is 30 seconds.
    read-timeout = 30000

    # Enables compatibility with HAProxy.
    proxy-protocol = false

    [query]
    # Whether to enable responding to GameSpy 4 query responses or not.
    enabled = false

    # If query is enabled, on what port should the query protocol listen on?
    port = 25577

    # This is the map name that is reported to the query services.
    map = "Velocity"

    # Whether plugins should be shown in query response by default or not
    show-plugins = false
