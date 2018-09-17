The Command API
==========================

What better way to expand your plugins using commands? This page will show how
to create, register and unregister commmands.


Create the command class
^^^^^^^^^^^^^^^^^^^^^^^^

Each command class implements the `Command interface <https://github.com/VelocityPowered/Velocity/blob/master/api/src/main/java/com/velocitypowered/api/command/Command.java>`_, which has two methods:
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

Now that we have created the command we need to register it in order for it to work.
We have a `Command Manager <https://github.com/VelocityPowered/Velocity/blob/master/api/src/main/java/com/velocitypowered/api/command/CommandManager.java>`_ where we can register our commands.
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

Creating a simple tab complete
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In this example we're going to be creating a simple command that will return how
many kills a player has (which are stored in a local hashmap for the purposes of
this tutorial). The player names who have kills will be able to be completed using
the tab key. 

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
    }

Let's break down the suggest method

.. code-block:: java

            if (currentArgs.length == 0) {
                return new ArrayList<>(playerKills.keySet());

Here the player has not typed a character after the command, so we will complete
using all the player names stored in the map.

.. code-block:: java

            } else if (currentArgs.length == 1) {
                return playerKills.keySet().stream()
                        .filter(name -> name.regionMatches(true, 0, currentArgs[0], 0, currentArgs[0].length()))
                        .collect(Collectors.toList());

Now the player has typed something, so we will suggest all the player names that
start with the characters that the player has typed. For instance, if the player
has typed ``Pla`` or ``Player``, it will suggest ``Player1`` and ``Player2``. If the
player has typed ``T``, it will suggest ``Tux``.

.. code-block:: java

             } else {
                return ImmutableList.of();
            }

Now here the player has tried to autocomplete another parameter of the command that
is no the first one, so we just return an empty list since our command has only one
argument.