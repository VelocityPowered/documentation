Configuring player information forwarding
=========================================

Velocity supports forwarding information about your players to your servers, such
as IP addresses, UUIDs, and skins. Velocity supports two different methods for
forwarding player information to your servers, which are described in the appropriate
sections.

Configuring modern forwarding
-----------------------------

``modern`` forwarding is a Velocity-native format. It forwards all player information
in an efficient binary format and employs a MAC code to make it much more difficult to
trick the server into impersonating your Velocity proxy. However, it is only available for
Minecraft 1.13 or higher.

To use modern forwarding with any supported server implementation, set the ``player-info-forwarding``
setting in ``velocity.toml`` to ``modern``. You must also change the ``forwarding-secret``
setting to a unique secret. You then need to ensure your server is properly configured to
use modern Velocity forwarding.

.. caution:: Modern forwarding, while more secure than the legacy BungeeCord forwarding
             scheme, is incompatible with Minecraft versions below 1.13, Minecraft Forge
             (both versions for Minecraft 1.12.2 and below and for 1.14 and above), and
             ProtocolSupport. If you support or rely on any of these, you will need to use
             legacy BungeeCord-compatible forwarding instead.

Paper
^^^^^

You will need build 377 and above for Paper 1.13 or any version of Paper 1.14+ to use modern
forwarding using Paper.

To allow Paper to understand the forwarded player data, in your ``paper.yml``, set
``settings.velocity-support.enabled`` to ``true`` and ``settings.velocity-support.secret``
to match the secret in your ``velocity.toml``. You must also set ``settings.velocity-support.online-mode``
to the ``online-mode`` setting in your ``velocity.toml``. Once you're done editing
``paper.yml``, reboot your server.

Fabric
^^^^^^

A mod called `FabricProxy <https://www.curseforge.com/minecraft/mc-mods/fabricproxy>` allows you to use
Velocity modern forwarding with a modded server using Fabric.


Configuring legacy BungeeCord-compatible forwarding
---------------------------------------------------

``legacy`` forwarding is the player information forwarding protocol that is used by
BungeeCord when enabling IP forwarding from BungeeCord. Due to this, it is ubiquitous
and well-supported by most server implementations. It has excellent compatibility
(supporting versions as old as 1.7.2, released in 2013) and will work with Forge if
you also install SpongeForge on your modded server and configure it correctly. However,
it is not as secure as the Velocity forwarding.

If you need to use legacy BungeeCord-compatible forwarding, simply set your ``player-info-forwarding``
setting in ``velocity.toml`` to ``legacy``. You will also need to make sure your server
is properly configured to understand the data sent by Velocity.

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
