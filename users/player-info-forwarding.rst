Configuring player information forwarding
=========================================

Velocity supports forwarding information about your players to your servers, such
as IP addresses, UUIDs, and skins. Velocity supports two different methods for
forwarding player information to your servers:

* ``modern`` forwarding is a Velocity-native format. It forwards all player information
  in an efficient binary format and ensures that nobody tries to trick the server into
  impersonating your Velocity proxy. However, it is only available for Minecraft 1.13
  or higher.
* ``legacy`` forwarding is the player information forwarding protocol used by BungeeCord.
  This is extremely compatible across all Minecraft versions that Velocity supports, but
  requires proper configuration to ensure that nobody pretends to be your proxy by using
  a firewall or a plugin like IPWhitelist.

Configuring modern forwarding
-----------------------------

Currently, only build 377 and above of Paper 1.13.1+ support Velocity's modern forwarding.

To use modern forwarding with any supported server implementation, set the ``player-info-forwarding``
setting in ``velocity.toml`` to ``modern``. You must also change the ``forwarding-secret``
setting to a unique secret. You then need to ensure your server is properly configured to
use modern Velocity forwarding.

.. caution:: Modern forwarding does not work with ProtocolSupport. Consider using legacy
             BungeeCord-compatible forwarding instead if you use ProtocolSupport.

Paper
^^^^^

To allow Paper to understand the forwarded player data, in your ``paper.yml``, set
``settings.velocity-support.enabled`` to ``true`` and ``settings.velocity-support.secret``
to match the secret in your ``velocity.toml``. You must also set ``settings.velocity-support.online-mode``
to the ``online-mode`` setting in your ``velocity.toml``. Once you're done editing
``paper.yml``, reboot your server.


Configuring legacy BungeeCord-compatible forwarding
---------------------------------------------------

If you need to use legacy BungeeCord-compatible forwarding, simply set your ``player-info-forwarding``
setting in ``velocity.toml`` to ``legacy``. You will also need to make sure your server
is properly configured to understand the data.

.. caution:: Legacy BungeeCord-compatible forwarding allows anyone to pretend they are your proxy
             and allow them to log in under any username or IP address! You must make sure that
             you have a firewall set up on your servers or use a plugin such as `IPWhitelist <https://www.spigotmc.org/resources/ipwhitelist.61/>`_
             to make sure your servers are protected.

Spigot / Paper
^^^^^^^^^^^^^^

To make Spigot or Paper understand the data forwarded from Velocity, set ``settings.bungeecord`` to
``true`` in your ``spigot.yml`` and then reboot your server.

Sponge
^^^^^^

To configure Sponge to understand the data forwarded from Velocity, set ``modules.bungeecord`` to ``true``
and ``bungeecord.ip-forwarding`` to ``true`` in your ``config/sponge/global.conf`` file, and then restart
your Sponge server.
