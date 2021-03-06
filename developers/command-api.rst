The Command API
==========================

The Command API lets you create commands that can be executed on the console or
via a player connected through the proxy.

Create the command class
^^^^^^^^^^^^^^^^^^^^^^^^

Each command class must implement the `Command interface <https://github.com/VelocityPowered/Velocity/blob/master/api/src/main/java/com/velocitypowered/api/command/Command.java>`_, which has two methods:
one for when the command is executed and one to provide suggestions for tab completion.
Let's see an example of a simple command that will tell whoever executes the command
"Hello World" in light blue text.

.. code-block:: java

    package com.example.velocityplugin;

    import com.velocitypowered.api.command.Command;
    import com.velocitypowered.api.command.CommandSource;
    import net.kyori.text.TextComponent;
    import net.kyori.text.format.TextColor;
    import org.checkerframework.checker.nullness.qual.NonNull;

    public class CommandTest implements Command {

        @Override
        public void execute(@NonNull CommandSource source, String[] args) {
            source.sendMessage(TextComponent.of("Hello World!").color(TextColor.AQUA));
        }
    }

Now that we have created the command, we need to register it in order for it to work.
To register commands, you use the `Command Manager <https://github.com/VelocityPowered/Velocity/blob/master/api/src/main/java/com/velocitypowered/api/command/CommandManager.java>`_.
We get the command manager by executing ``proxyServer.getCommandManager()`` with 
the proxy instance, or by injecting it using the ``@Inject`` annotation in our
main class. The register method requires two parameters, the command object and 
the command aliases which is a varargs parameter.

.. code-block:: java

    commandManager.register(new CommandTest(), "test");

If we assemble it all into our main class created on the first tutorial, it'll look
something like this

.. code-block:: java

    package com.example.velocityplugin;

    import com.google.inject.Inject;
    import com.velocitypowered.api.command.CommandManager;
    import com.velocitypowered.api.plugin.Plugin;
    import org.slf4j.Logger;

    @Plugin(id = "myfirstplugin", name = "My First Plugin", version = "1.0-SNAPSHOT",
            description = "I did it!", authors = {"Me"})
    public class VelocityTest {

        @Inject private VelocityTest(CommandManager commandManager, Logger logger) {
            commandManager.register(new CommandTest(), "test");
            logger.info("Plugin has enabled!");
        }
    }

As you can see we're injecting the commandManager instance but we can also obtain
it by injecting the ``ProxyServer`` and getting it from there.

How command arguments work
^^^^^^^^^^^^^^^^^^^^^^^^^^

The execute method has a ``String[]`` which represents the arguments of the command.
The arguments don't include the base command. It is important to note that in the
event that no arguments are specified, an empty array will be passed, rather than
a null array. 

If a player or a console executes the following command: ``/stats Player2 kills``,
the first argument will be ``Player2``, which we can access using ``args[0]`` and
the second argument will be ``kills``.

Let's create a command that will return how many kills a player has (which are
stored in a local hashmap for the purposes of this tutorial).

The command will be ``/stats <player>``

.. code-block:: java

    package com.example.velocityplugin;

    import com.google.common.collect.ImmutableList;
    import com.velocitypowered.api.command.Command;
    import com.velocitypowered.api.command.CommandSource;
    import net.kyori.text.TextComponent;
    import net.kyori.text.format.TextColor;
    import org.checkerframework.checker.nullness.qual.NonNull;

    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.stream.Collectors;

    public class TabCompleteTest implements Command {

        private final Map<String, Integer> playerKills = new HashMap<>();

        public TabCompleteTest() {
            playerKills.put("Tux", 58);
            playerKills.put("Player2", 23);
            playerKills.put("Player3", 17);
        }

        @Override
        public void execute(@NonNull CommandSource source, String[] args) {
            if (args.length != 1) {
                source.sendMessage(TextComponent.of("Invalid usage!").color(TextColor.RED));
                source.sendMessage(TextComponent.of("Usage: /stats <player>").color(TextColor.RED));
                return;
            }

            String playerName = args[0];
            if (playerKills.containsKey(playerName)) {
                source.sendMessage(TextComponent
                        .of(playerName + " has " + playerKills.get(playerName) + " kills.")
                        .color(TextColor.GREEN));
            } else {
                source.sendMessage(TextComponent.of("Player not found").color(TextColor.RED));
            }
        }
    }

Let's break down the command.

.. code-block:: java

        private final Map<String, Integer> playerKills = new HashMap<>();

        public TabCompleteTest() {
            playerKills.put("Tux", 58);
            playerKills.put("Player2", 23);
            playerKills.put("Player3", 17);
        }

We create a simple map where we'll store dummy players with kills as an example for
this tutorial. If you were to create a stat plugin, these players would be loaded
from the database or from another file.

.. code-block:: java

        @Override
        public void execute(@NonNull CommandSource source, String[] args) {
            if (args.length != 1) {
                source.sendMessage(TextComponent.of("Invalid usage!").color(TextColor.RED));
                source.sendMessage(TextComponent.of("Usage: /stats <player>").color(TextColor.RED));
                return;
            }

We first check that the arguments are equal to 1, meaning they specified a player.

.. code-block:: java

                String playerName = args[0];

We get the player name that was provided in the command. ``/stats Player2``, the
``playerName`` would be ``Player2``.

.. code-block:: java

            if (playerKills.containsKey(playerName)) {
                source.sendMessage(TextComponent
                        .of(playerName + " has " + playerKills.get(playerName) + " kills.")
                        .color(TextColor.GREEN));
            } else {
                source.sendMessage(TextComponent.of("Player not found").color(TextColor.RED));
            }

Finally do a simple check to see if the player has kills and display them if they
do have, or otherwise send them a message that the player is not found.


Creating a simple tab complete
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Tab completion is when a player or the console presses the tab key while writing
a command, in which the plugin will automatically give suggestions according to the
context of the command. Let's say you're typing ``/kill`` and then press the tab
key, the plugin would suggest the names of the players who are online.

We'll base on the last command example, but will add one thing. The player names
who have kills will be able to be completed using the tab key. 

.. code-block:: java

        @Override
        public List<String> suggest(@NonNull CommandSource source, String[] currentArgs) {
            if (currentArgs.length == 0) {
                return new ArrayList<>(playerKills.keySet());
            } else if (currentArgs.length == 1) {
                return playerKills.keySet().stream()
                        .filter(name -> name.regionMatches(true, 0, currentArgs[0], 0, currentArgs[0].length()))
                        .collect(Collectors.toList());
            } else {
                return ImmutableList.of();
            }
        }

Let's break down the ``suggest`` method.

.. code-block:: java

            if (currentArgs.length == 0) {
                return new ArrayList<>(playerKills.keySet());

If the player hasn't entered anything other than the command, we will suggest all
the names in the map.

.. code-block:: java

            } else if (currentArgs.length == 1) {
                return playerKills.keySet().stream()
                        .filter(name -> name.regionMatches(true, 0, currentArgs[0], 0, currentArgs[0].length()))
                        .collect(Collectors.toList());

Now the player has typed something, so we will suggest all the player names that
start with the characters that the player has typed. For instance, if the player
has typed ``Pla`` or ``Player``, it will suggest ``Player2`` and ``Player3``. If
the player has typed ``T``, it will suggest ``Tux``.

.. code-block:: java

             } else {
                return ImmutableList.of();
             }

If the player tries to autocomplete more than one argument, we return an empty
list since our command only has one argument.
