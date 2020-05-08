Frequently asked questions
==========================

What versions of Minecraft does Velocity support?
-------------------------------------------------

Velocity supports Minecraft 1.8-1.15. It is important to note, however, that
Velocity does not translate between protocol versions - most packets from
the client and server are passed through the proxy unchanged. If you need
a multi-protocol solution for your Minecraft server, please consider installing
`ProtocolSupport <https://www.spigotmc.org/resources/protocolsupport.7201/>`_
or `ViaVersion <https://www.spigotmc.org/resources/viaversion.19254/>`_
on your backend servers.

What server software is supported by Velocity?
----------------------------------------------

Velocity aims to support Paper, Sponge, and Minecraft Forge. As of September 7,
2018, Forge support is available and the proxy has been most extensively tested
against Paper, although Sponge also runs well.

Is Velocity compatible with my Forge mod(s)?
--------------------------------------------

Velocity is compatible with Minecraft Forge (1.8-1.12.2) and its legacy player
information forwarding is compatible with SpongeForge. Most mods should work
without issue and with less issues than with BungeeCord or Waterfall.

However, there are certain mods that are incompatible with the server-switching
behavior Velocity employs. These are issues that only the author of the mod can
fix, and are not issues with Velocity.

What is Velocity's performance profile?
---------------------------------------

On a Velocity server without plugins, most CPU time is spent processing packets
(especially decompressing and recompressing) and waiting on network events.
Velocity has been tuned for throughput: given enough resources, a single proxy
should be able to handle a large number of Minecraft players online.

There are several ways to increase the throughput of the proxy.

Keep an eye on your plugins
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The biggest performance killer by far are your plugins! Velocity implements
several measures to attempt to reduce issues caused by misbehaving plugins, but
these measures are imperfect. It is important you monitor your plugins to
ensure they are not hurting your proxy throughput.

Disable compression between the proxy and your backend server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If your backend server has compression enabled (by default, Minecraft servers
compress packets larger than 256 bytes), then Velocity is forced to decompress
the packets from servers so it can process them, usually only to compress then
shortly afterwards because it did not find anything interesting. To eliminate
this inefficiency, you should disable compression on your backend server, so
that only Velocity is responsible for compressing packets.

To disable compression, simply set ``network-compression-threshold=-1`` in your
``server.properties``, and then reboot your server.

Keep up to date
^^^^^^^^^^^^^^^

The Velocity team constantly seeks to improve the throughput of the proxy, and
you can only benefit from our efforts if you keep the proxy regularly
up-to-date.
