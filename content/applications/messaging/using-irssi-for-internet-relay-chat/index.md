---
slug: Using Irssi for Internet Relay Chat (IRC)
description: 'Use IRC in conjunction with GNU Screen for maintaining persistent connections to IRC networks.'
keywords: ["irssi", "irc", "oftc", "freenode", "real time", "chat"]
modified: 2024-06-17
modified_by:
  name: Pawan Kumar
published: 2024-06-17
title: Using Irssi for Internet Relay Chat
authors: ["Pawan Kumar"]
---
**Irssi** is a terminal-based chat client designed for real-time conversations using Internet Relay Chat (IRC). IRC serves as a central hub for Utho users to share knowledge and troubleshoot issues in our public channel, #Utho, hosted on OFTC.

You can run Irssi on Linux or macOS, either locally on your workstation or on your Utho. If you're new to using a Linux terminal, we recommend reviewing Utho's guides on Using the Terminal and Introduction to Linux Concepts. Ensure you have completed our guide on Setting Up and Securing a Compute Instance if you plan to install Irssi on your Utho.

## Prerequisites

Before you begin, complete these tasks:

Follow our Setting Up and Securing a Compute Instance guide to update your system. You may also want to set the timezone, configure your hostname, create a limited user account, and secure SSH access.

Ensure that GNU Screen is installed. It typically comes pre-installed, but you can refer to our Screen Guide for more information.

## Installing Irssi

To install Irssi, use the command that corresponds to your operating system:

Debian or Ubuntu:

                              apt-get install irssi

CentOS or Fedora:

                               yum install irssi

Arch:

                                pacman -S irssi

Mac OS X with MacPorts:

                                 port install irssi

Before installing Irssi on macOS using Homebrew, ensure that you have updated Homebrew to include the latest formulas. Use the following commands to update Homebrew and install Irssi:

                                 brew update
                                 brew install irssi

## Starting Irssi

To begin using Irssi, follow these steps:

Initiate a new Screen session named **chat** to ensure Irssi remains active even after closing your terminal session:

                                  screen -S chat

Start Irssi:

                                   irssi

You will see the Irssi startup screen appear on the default chat interface.

To reconnect to Irssi later, simply rejoin your Screen session.

## Configuring Irssi

Next, configure Irssi to connect to your desired networks, join channels, and set your nickname (nick). Here's how to connect to the Utho IRC channel, #Utho:

### Joining IRC Networks

The Utho channel is hosted on the OFTC network. An IRC network comprises one or more interconnected IRC servers sharing the same channels and users. Utho's channel resides on irc.oftc.net. Join the OFTC network using the following command within Irssi:

                                        /connect irc.oftc.net

To disconnect from a channel, run:

                                         /disconnect irc.oftc.net

Note: You can connect to other networks by substituting irc.oftc.net with a different network address.

### Joining Channels

A channel functions as a chatroom. Utho's channel is named #Utho. Use the command below to join:

    /join #Utho

After joining the channel, you can begin sending messages that everyone in the channel will see.

To join a channel that requires a password, use the following command, replacing #channel with the channel name and password with the channel password:

                                     /join #channel password

To leave or exit an IRC channel, use the following command:

                                     /part #Utho

### Configuring Default Nickname (Nick)

To set your default nickname, use the command below, replacing user with your desired nickname:

                                      /set nick user

If the nickname you've chosen is already in use on the servers you're connected to, Irssi will notify you.

### Managing IRC Nicknames

To change your IRC nickname at any time, simply use the /nick command again. Replace user with your new nickname:

                                      /nick user

If the nickname you choose is already in use on the servers you're connected to, Irssi will notify you.

When you change your nickname, every channel you're in will display a message notifying everyone of the change.

To gather more information about an IRC user, use the command below:

                                      /whois new_user

Sample output from the `whois` command:

                          11:45 -!-  new_user [for@example.com]
                          11:45 -!-  ircname  : Cecil Sharp
                          11:45 -!-  channels : #Utho
                          11:45 -!-  server   : sample.oftc.net [Galloway, NJ, US]
                          11:45 -!-           : user has identified to services

The information retrieved through this command includes the IRC nickname, channels the user is currently in, and the real name of the user.

### Sending Messages

To send a private message to a specific person on your network, use the `/msg` command. Replace `friendnick` with the recipient's nickname and `Hello there!` with your message:

                           /msg friendnick Hello there!

Note: If you are in a channel, you can use the tab key to autocomplete nicks within that channel.

Messages sent through Irssi are `not` encrypted and should not be considered secure communications. Additionally, Irssi does not have a built-in spellcheck feature.

### Basic Window Navigation

To navigate between different channels and private messages, use the command:

                             /win number

For instance, if you joined the #Utho channel on Window 2, you can return to it using the command:

                              /win 2

### Managing and Manipulating Windows

To switch between windows in Irssi, use the `Alt` key along with the corresponding window number. Windows numbered 1 through 10 are accessible via `Alt-1` through `Alt-0` on your keyboard. For windows numbered 11 through 19, use Alt-q through `Alt-o`. Irssi supports more than 19 windows, but windows numbered 20 and higher aren't directly accessible through key bindings. Use the command below to navigate between windows:

                              /win number

Note: If `Alt` + `num` doesn't switch windows, try Esc followed by the window number.

Here are additional commands for navigating between windows:

- `/win list` - Displays a detailed list of all windows. Sample output is shown below.
- `Alt+A` (/window goto active) - Moves focus to the window with the highest activity and lowest identifier.
- `Ctrl-n` (/window next) - Shifts focus to the next window in sequence.
- `Ctrl-p` (/window previous) - Moves focus to the previous window in sequence.
- `/wc` (/window close) - Closes the currently selected window.

### Adding Default Networks

To automatically join a network when Irssi starts, use the following command:

    /server add -auto -network OFTC irc.oftc.net 6667

Here's what each part of the command does:

- The -auto flag instructs Irssi to connect to the server automatically on startup.
- The -network flag assigns a name (OFTC in this case) to the network, useful for managing multiple servers under the same network name.
- irc.oftc.net specifies the server address to connect to.
- Optionally, you can specify a port to connect to the server. If left blank, Irssi will use the default port. It's recommended to use an encrypted port option if available.

  You can add as many channels as needed to this command.

## Irssi Commands and Usage

In Irssi, all commands begin with a slash (/). Each channel you join and any private messages you receive will appear in separate windows. The active window's name is displayed at the prompt on the left-hand side.

If you're expecting output from a command but don't see it, return to your **(status)** channel.

Here are some Irssi commands:

| Command | Description                                                                      |
|:---------------|:--------------------------------------------------------------------------|
| /ban           | Sets or lists bans for a channel                                          |
| /clear         | Clears a channel buffer                                                   |
| /disconnect    | Disconnects from the network that has focus                               |
| /exit          | Disconnects your client from all networks and returns to the shell prompt |
| /join          | Joins a channel                                                           |
| /kick          | Kicks a user out                                                          |
| /kickban       | Kickbans a user                                                           |
| /msg           | Sends a private message to a user                                         |
| /names         | Lists the users in the current channel                                    |
| /query         | Opens a query window with a user or closes a current query window         |
| /topic         | Displays/edits the current topic                                          |
| /unban         | Unbans everyone                                                           |
| /whois         | Displays user information                                                 |
| /window close  | Forces closure of a window                                                |
|---------------------------------

## Disconnecting and Exiting Irssi

To disconnect from an IRC network, use this command, replacing **freenode** with your network:

    /disconnect freenode

Exit the Irssi program using the command

                                         /exit

## Configuring Hilights

To `highlight` certain words or topics in channels you've joined, use the hilight command:

                                        /hilight word

To remove a `highlight`, use:

                                       /dehilight word

## User-friendly Plugins

Enhance your Irssi experience with user-friendly plugins! Explore features like adding a list of open windows, colored nicks, and more. Check out the Using Plugins section of the Advanced Irssi Usage guide.
