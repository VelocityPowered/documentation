Getting started with Velocity
=============================

Velocity is refreshingly easy to set up.

.. warning:: Velocity is in an alpha-quality state and is not yet suitable for production use.


Installing Java
---------------

Velocity is built on Java, so if you do not already have Java installed, you
will need to install it before you continue. A discussion about installing Java
is out of scope for the Velocity documentation to cover.

Downloading Velocity
--------------------

You will need to download Velocity first. Visit the `download page <https://www.velocitypowered.com/downloads>`_
and download the latest proxy build from it. Place the downloaded JAR file
into a directory just for your proxy. Afterwards, you can run the JAR using
``java -jar velocity-proxy-1.0-SNAPSHOT-all.jar``.

Configuring Your Servers
------------------------

Once Velocity is up and running, we can move on to configuring your servers
for use with Velocity. For now, we're going to get a basic setup going and
improve upon it later.

Open up ``velocity.toml`` and find the ``[servers]`` section. This section looks
like this:

.. code-block:: plaintext

    [servers]
    lobby = "127.0.0.1:30066"
    factions = "127.0.0.1:30067"
    minigames = "127.0.0.1:30068"

Go ahead and put your servers in this file, and then restart Velocity. Once you've
done that, you will need to open the ``server.properties`` file for each of your
servers and set the ``online-mode`` setting to ``false``. This allows Velocity
to connect to your server. Once you're done, you should restart your server.
Velocity should now be ready to use.

However, this is a a minimal setup. Notably, since we're not forwarding IPs and
player information, the Minecraft server will assume you connected from offline
mode. Velocity supports forwarding this information. See :doc:`./player-info-forwarding`
for more information about that.

What's Next?
------------

In this section, you downloaded and added your servers to the ``velocity.toml``
file. This file is very important for us, so in the next section we'll cover it
in great detail.