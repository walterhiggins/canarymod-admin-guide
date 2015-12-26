# Canarymod Admin Guide

## Preface
This is a copy of the original CanaryMod Administrator's Guide which was written and maintained by the CanaryMod Team up to August 2015.
All words and images are copyright of the [CanaryMod Team][team]. 

Unfortunately the CanaryMod team no longer host or maintain the admin guide. This copy is from the Internet Archive https://web.archive.org/web/20150427052518/http://canarymod.net/books/canarymod-admin-guide and is provided as-is for use by readers of the book [A Beginner's Guide to Writing Minecraft Plugins in Javascript][book]

[book]: http://www.peachpit.com/store/beginners-guide-to-writing-minecraft-plugins-in-javascript-9780133930146

[team]: https://github.com/CanaryModTeam

## Introduction
In this book we will set up a server and introduce you to the ways how the server mod works, where to find files and why.

We will also introduce you to the management plugins and the GUI tool to configure your server.

## Table of Contents

### [Setting up your Server][setup]

This is a general run down of how things are structured and some info about major configuration elements.

### [Permissions and Groups][perms]

Yea that's right Canary has permissions and groups build-in. This page talks about them and setting them up.

### [Plugin Priorities for Admins][plugins]

Need to make sure your land protection Plugin gets to protect the land?

### [Commands][commands]

Need to know more about a command well here is your place to do that.

### [List of Permissions][permlist]

So yea we have permissions, but now you say what are the permissions. Well here they are!

[setup]: #setting-up-your-server-1
[perms]: #permissions-and-groups-1
[plugins]: #plugin-priorities-for-admins-1
[commands]: #commands-1
[permlist]: #list-of-permissions-1

## Setting up your Server
So, what do you need for your server?

Exactly one thing: CanaryMod.jar

Since that is all, lets begin.

### The folder structure

CanaryMod uses a couple of files and directories, here's a listing to get you an overview.

* lib/ The place where libraries and external dependencies for Plugins are
* db/ If you're not using MySQL as datasource, your database file will be in here.
* config/ Every configuration file can be found here, specifically net.cfg and server.cfg (More on specific cfg files later)
* config/worlds This folder specifically contains configurations for each world you create
* worlds/ Here are all your worlds stored
* plugins/ Your plugins must be stored in here

### Configuring the server

Now comes the fancy part of all the server stuff. The configuration. We moved all configurations from server.properties to their respective cfg files. That's to keep the configuration files nice and clean. In order to get all the files, run CanaryMod once, so it will create everything for you in the right place, once the first launch has succeeded you can shut down the server angain and start working on your configurations. CanaryMod has deployed reasonable default values already, but you may adjust them to your liking.

### The server.cfg

Here you configure the basic settings of your server, however, everything world related has been moved, we will handle this later. Here's a list of configurations you find in server.cfg.

* debug - Show also debugging messages in the server.log (true/false)
* reservelist - Use the reserverlist or not (true/false)
* playerlist-enabled - Enable the player list (on tab) (true/false)
* default-ban-message - The default ban reason to show to banned users trying to connect
* data-source - The datasource you want to use (currently flatfile and mysql)
* logging - Enable logging to the log file(true/false)
* playerlist-autoupdate - Auto-update the player tab list? (true/false)
* playerlist-ticks - Ticks between playerlist updates
* playerlist-usecolors - Use player colors in the playerlist?(true/false)
* default-world-name - The name of the world that is loaded by default
* show-unknown-command - Show "Unknown command" or not when command isn't known? (true/false)
* death-message - Show the death messages on player death? (true/false)
* whitelist - Use the whitelist? (true/false)
* whitelist-message - The message to show players that are not on the whitelist
* motd - The motto of the day message
* port - The port to use this server
* enable-query - Enable or disable querying for usage statistics (true/false)
* query.port - The port to listen on for queries (TODO: wtf? really?)
* enable-rcon- Allow rcon commands
* online-mode -Use online mode or not (recommended that you do!)
* server-ip - Bind the server to this specific IP. If you run a dedicated server, you should not use this
* max-players - May amount of players
* rcon.port - The port to listen on for rcon commands
* rcon.password - The RCON password
* view-distance - The view-distance (amounts of chunks sent to the player on a distance)
* language - The language of your interface (command descriptions et cetera). Default is en_US
* date-format - Formatting of timestamps (for showing people ban times etc)
* commandblock-commands - List of commands the commandblock is allowed to execute
* enable-command-block - Enables the use of command blocks
* max-players - The max amount of players on this server.

There are more properties from the vanilla server. You can put them all in here if you miss one. It will be loaded and used respectively.

### Database

If you now look into your db/ folder you will find databases there. That is, if you're not using mysql. If you use flatfile, you could now go and edit all the files by hand. That's perfectly fine although might be a bit cumbersome compared to the old CanaryMod files but once you get the hang of it, it's okay. If you don't feel like getting a hang for it, no problem. We also provide a Tool and ingame and concosle commands to manage the data! In case you feel the crazy urge to inspect the database, here's a list of tables (or files) and what sort of data they contain.

* player - Contains all registered users. Users not in this list will get the default group assigned!
* group - Contains all groups of your server.
* permission - This table contains all existing permissions, their values and who they belong to.
* ban - Contains bans and ban related information
* kit - Contains kits
* warps - Contains warp points and player homes

In the next chapter we will have a go at editing permissions and groups with the commands CanaryMod offers you.

## Permissions and Groups

CanaryMod sports a system to store permissions which is there to avoid data junk on your server. It makes sure that every permission exists only once or max. twice. One for player, one for group. Additionally, this removes any incompatibility that would have been introduced by external permissions management.

In this chapter, I want to go through the commands to manage players and groups. CanaryMod comes with everything you need to set up yourserver from console or from in-game.

By default, we have a bunch of registered dummy users and four groups, that have a set of basic permissions. You can remove them all and start from scratch or use them as basis to establish something to suit your needs. Changes you make are unbuffered that means, if you change something it will be applied immediately to online and offline players.

While editing groups, please remember there MUST be a default group defined. This group will be automatically assigned to new-spawned players or players you didn't explicitly add to your users list. You can define those by setting the "isDefault" property to your group or by simply making it inherit from itself. We will use the latter method in this chapter.

### What are groups?

Simply speaking, groups are sets of permissions that can be applied to a number of players. Each group can have a so called parent group. A parent because if Group A inherits from Group B, then it will have all permissions from Group B plus its own. This way you can easily create permission hierarchy structures so that players to moderators to administrators have increasing access levels, without redefining all the small commands for each group.

Commands for editing and managing groups are stored in the /groupmod command set. Alternatively you can use the shortcut "/group"

### Removing groups

Removing groups is very easy.

```
/groupmod remove groupName
```

Where groupName is the name of the group to remove. Players that have this group, will automatically get its parent group assigned. If it has no parent group, the default group will be assigned. You cannot delete the default group, however you can do other things with it.

### Adding groups

You can add a group in two ways. One way will not define a parent group, the other does not. It's recommended to always define a parent group so a solid permission hierarchy can be established and removing a group will not impact your data integrity.

```
/groupmod add Builders defaultgroup
```

This will create a goup Builders that inherits permissions from defaultgroup. If you don't want to add a parent, just leave it blank. Additionally, you can specify a world in which this group can be applied to a player. For instance, if you have a multi-world setup and wish to give moderator access only in a specified world. This is done by given a world name to the "add" command.

```
/groupmod add Builders worldName
```

This makes the group Builders only applicable in the mworld with the given name. You can also specify a group hierarchy as usual. However, the parent group MUST be applicable to the same world as the group you want to add.

```
/groupmod add Builders Players worldName
```

Now Builders inherits from Players and those are only applicable in the given world.

### Renaming a Group

You have this group there, it has established permissions and is part of a hierarchy chain. It's all great but maybe its name is incorrect or doesn't suit its purpose anymore. In this case you can simply rename your group to something else.

```
/groupmod rename wrongNamedGroup newGroupName
```

It's easy as that.

### Editing Permissions

Now to the important thing. Editing permissions. Some of you might fear the new permission stuff in CanaryMod a little but you will see, it's as easy as droppign a command. There are two things you can do. Add or remove a permission. Adding a permission that already exists will update the permission automatically. Here's how:

```
/groupmod permission add group canary.command.help
```

Will add the permission to use the Help command to the given group. by default a permission value will be "true". That means allowed. You can explicitly set a permission value to "false" in order to (explicitly) deny a certain permission. This is useful for shorter permission sets. You can allow a whole range of permissions, and just explicitly deny a few. Using a value works like this:

```
/groupmod permission add Group canary.command.super.ban:false
```

To show you an example of allowing a range and denying a few, look at this:

```
/groupmod permission add Moderators canary.command.*
/groupmod permission add Moderators canary.command.ban:false
```

Allows all super commands but ban.

In order to remove a permission you can set it to false (like in the example above) or use the word "remove" instead of add.

```
/groupmod permission remove Moderators canary.command.*
```

Now moderators will have no more super commands.

Now, you might have a lot of groups and they have a lot of commands and you can not or don't want to remember all of them. For this purpose, there are list and check commands. Personally, I suggest using the check command. You can give it a permission and it will check for you, and tell you, if that group has this permission and if so, which value it has. Here's how to work it:

```
/groupmod permission check Players canary.command.*
```

If you require a list of all permissions, you can do this:

```
/groupmod permission list players
```

This displays all permissions of the players group.

### Player Permissions

Player permissions are a lot like group permissions. There is one important fact to note: Per-player permissions will take precedence over group permissions. That means, if you grant a certain permission to a group, you can explicitly deny it for any given player - or vice versa.

```
/playermod permission add playerName canary.command.help
```

As you can see here the permission subcommand of playermod works the same as it does for groups the only thing that has changed is that you give it the player's name and not a group's name.
 

### Adding and Removing a Player from a Group

To be able to set the group a player is part of use this:

```
/playermod group set playerName groupName
```

This here sets the players main group meaning that that player has the all of the permissions that the group is made to give.

Now to remove a Player from the "main" group you will need to use:

```
/playermod group set playerName default
```

This sets the player back to the default group, effectively removeing them from the group.

You can put the player in multiple groups if you wish. They do not need to be in any particular hierachical order. Important note: "Extra-Groups" take precendence over the main group. That means you can explicitly deny a command that was allowed in the main group or vice versa.

### Player Prefixes

To change the prefixe of a player use:

```
/playermod prefix playerName prefix
```

The Prefix can be anything you want, color codes can be included also. 

And that should be about it. Remember: There's also help content available in-game and via console, describing how to use the commands.

### OPs

Canary now supports use of Ops for use as an admin.

## Plugin Priorities for Admins

Since CanaryMod determines the correct order of how plugins are loaded automatically, it is possible that some plugins receive hooks before others and you actually don't want that. In Canary Classic, there was a plugins list in your server.properties where you could put the order of your plugins how you want it. However, since we do dependency resolving we can not let you do that anymore (sorry 'bout that). We have another way for you to fit those things in.

In your config/ folder you will find a plugin_priorities.cfg which contains names of all your plugins and a number. With this number you can define which plugin gets a hook before another. Higher numbers mean earlier execution!

It's as easy as fitting your numbers. If you want Plugin B to get the hook before Plugin A, then give it a 100, and Plugin A a 50.

If you don't want to auto-load a certain plugin at server start, give it a negative number!

That's all there is to it.

## Commands

Incomplete

## List of Permissions

This is a list of all the permissions related to CanaryMod. This list does NOT contain any permissions from plugins.

### CanaryMod Permissions

#### "Super" Permissions

* canary.command.ban - Allow banning of players
* canary.command.ipban - Allow IP Banning
* canary.command.kick - Allow kicking of other players
* canary.command.mute - Allow muting and unmuting of other players
* canary.command.reload - Allow reloading of all configurations (includes managed plugin configurations)
* canary.command.setspawn - Allow setting the (real) world spawn of the current world
* canary.command.groupmod - Basic usage of groupmod (does not need to be set)
* canary.command.groupmod.add - Allow adding of new groups
* canary.command.groupmod.delete - Allow removing of groups
* canary.command.groupmod.permissions - Allow editing group permissions
* canary.command.groupmod.permissions.add - Allow adding new permission nodes
* canary.command.groupemod.permissions.remove - Allow removing permission nodes
* canary.command.groupmod.permissions.check - Allow check of permission nodes on groups
* canary.command.groupmod.permissions.list - Allow listing of all permission nodes on groups
* canary.command.groupmod.permission.flush - Allow flushing all permission nodes on groups (refresh with database data)
* canary.command.groupmod.list - Allow viewing a list of all groups
* canary.command.groupmod.rename - Allow renaming of groups
* canary.command.groupmod.prefix - Allow changing a groups prefix
* canary.command.playermod - basic playermod usage (does not need to be set)
* canary.command.playermod.add - Allow adding of new player data
* canary.command.playermod.remove - Allow removing of player data
* canary.command.playermod.permissions - Allow changing player permissions
* canary.command.playermod.permissions.add - Allow adding new permission nodes to players
* canary.command.playermod.permissions.remove - Allow removing permission nodey from players
* canary.command.playermod.permissions.check - Allow checking for permission nodes on players
* canary.command.playermod.permissions.list - Allow listing all permission nodes of players
* canary.command.playermod.prefix - Allow changing player prefixes
* canary.command.playermod.group - Allow changing a players group
* canary.command.reservelist - Allow editing of the reservelist
* canary.command.whitelist - Allow editing of the whitelist
 

#### "Player" Permissions

* canary.command.compass - Allow using the compass command
* canary.command.getpos - Allow using the /getpos command
* canary.command.give - Allow a player to use the give command for him- or herself
* canary.command.give.other - Allow using the /give command to give to others
* canary.command.kill - Allow a player to use the kill command on him- or herself
* canary.command.kit - Allow a  player to use the kit command for him- or herself
* canary.command.kit.other - Allow a player to use the kit command to give kits to others
* canary.command.kit.group - Allow the creation of kits specifically for groups
* canary.command.kit.private - Allow the creation of kits specifically for a single person or a group of people
* canary.command.mobspawn - Allow spawning of mobs
* canary.command.msg - Allow usage of private messages
* canary.command.list - Allow viewing the list of players online

#### "Teleport" Permissions

* canary.command.home - Allow using homes
* canary.command.home.other - Allow visiting other players homes
* canary.command.home.set- Allow setting a home
* canary.command.spawn - Allow teleporting to a worlds spawn
* canary.command.self - Allow teleporting to other players
* canary.command.other - Allow teleporting others

#### "Warp" Permissions

* canary.command.warp.list - Allow viewing a list of available warps. This list depends on if a player can use a warp or not
* canary.command.warp.set - Allow creation of new warps
* canary.command.warp.set.admin - Allow overriding of existing warps and home locations
* canary.command.warp.set.public - Allow creation of public warps
* canary.command.warp.set.group - Allow creation of group warps
* canary.command.warp.set.private - Allow creation of private warps
* canary.command.warp.use - Allow the usage of warps
* canary.command.warp.admin - Allows usage of all warps regardless of other permissions

#### "Plugin" Permissions

* canary.command.plugin.enable - Allow enabling of plugins
* canary.command.plugin.disable - Allow disabling of plugins
* canary.command.plugin.reload - Allow reloading of plugins 

#### Other Permissions

* canary.command.motd - Allow viewing the motto of the day
* canary.command.help - Allow viewing the help pages
* canary.world.commandblock - Usage of Commandblock
* canary.world.spawnbuild - Allow building around spawn area
* canary.world.build - Allow building at all
* canary.super.administrator - Marks a player as administrator (warning: this permission has security implications, only give it to trusted users)
 

### Vanilla Permissions

* canary.command.achievement - Usage of achievement and statistics editing
* canary.command.broadcast - Broadcast messages
* canary.command.clear - Clear inventory
* canary.command.debug - Allow start/stop of debugging sessions (warning: this permission has security implications, only give it to trusted users)
* canary.command.defaultgamemode - Allows usage of the default game mode command (warning: this permission has security implications, only give it to trusted users)
* canary.command.setworldspawn - Allows changing of the world spawn position
* canary.command.difficulty - Allow changing of difficulty
* canary.command.effect - Allow managing of status effects on players
* canary.command.emote - Allow usage of the emote command
* canary.command.enchant - Allow usage of the enchant command
* canary.command.gamemode - Allow usage of the gamemode command
* canary.command.gamerule - Allow managing of game rules
* canary.command.give - Allow usage of the give command (see player permissions)
* canary.command.message - Allow private messaging via tell or msg command
* canary.command.playsound - Allow usage of the playsound command
* canary.command.save.all - Permission to issue the save-all command
* canary.command.save.off - Permission to turn off world-data saving (warning: this permission has security implications, only give it to trusted users)
* canary.command.save.on - Permission to turn on world-data saving (warning: this permission has security implications, only give it to trusted users)
* canary.command.scoreboard - Permission to use the scoreboard command
* canary.command.setblock - Permission to set blocks via command
* canary.command.spawnpoint - Permission to set a player-based spwan point
* canary.command.spreadplayers - Permission to use the spreadplayers command
* canary.command.summon - Permission to summon (spawn) mobs
* canary.command.teleport - Permission to allow teleporting
* canary.command.teleport.other - Permission to allow teleporting of others
* canary.command.testfor - Permission to use the testfor command
* canary.command.testforblock - Permission to use the testforblock command
* canary.command.time - Permission to handle game time
* canary.command.toggledownfall - Permission to toggle weather on and off
* canary.command.weather - Permission to specify the weather
* canary.command.xp - Permission to manage the experience points of a player
* canary.command.time - Permission to handle game time