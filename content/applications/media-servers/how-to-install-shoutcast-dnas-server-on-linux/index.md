---
slug: "Setting Up Shoutcast DNAS Server on Linux: A Step-by-Step Guide"
description: "Learn How to Set Up and Configure a SHOUTcast DNAS Server for Media Streaming on Linux"
keywords: ["shoutcast", " internet radio", " streaming media", " streaming audio"]
modified: 2024-05-06
modified_by:
  name: Utho
published: 2024-05-06
title: How to Install A SHOUTcast DNAS Server on Linux
dedicated_cpu_link: true
authors: ["Utho"]
---

How to Install A SHOUTcast DNAS Server on Linux title graphic

SHOUTcast software is tailored for streaming media online, operating on a classic client-server setup. By installing SHOUTcast on your server, you can broadcast music streams to connected clients. Since Shoutcast servers often require ample disk space, consider leveraging our [Block Storage](/docs/products/storage/block-storage/) service with this setup.

Note: Make sure to v[check the broadcast tools download page](http://www.shoutcast.com/broadcast-tools) to get the latest SHOUTcast version.

## SHOUTcast DNAS Software

The SHOUTcast DNAS (Distributed Network Audio Server) software functions as the server component enabling broadcasting to listeners. To get started with SHOUTcast, you'll first need to download and install SHOUTcast DNAS on your utho.

### Download and Install SHOUTcast

Ensure you're downloading the correct version, as there are multiple variants available. The Linux edition is provided in both 32-bit and 64-bit variations. [download](http://www.shoutcast.com/broadcast-tools) the version that corresponds to your utho's operating system.

Create a dedicated user to run SHOUTcast, avoiding root access. Use the following command:

        adduser shoutcast

Switch to the newly created user's home directory:

        cd /home/shoutcast

Create a directory for SHOUTcast:

        mkdir sc

Now, download the DNAS package. For this example, let's fetch the 32-bit version:

        wget http://download.nullsoft.com/shoutcast/tools/sc_serv2_linux_x64-latest.tar.gz

Unpack the SHOUTcast files into the designated directory:

        tar -xzf sc_serv2_linux_x64-latest.tar.gz -C sc

Adjust ownership from `root` to the SHOUTcast user:

        chown -R shoutcast.shoutcast /home/shoutcast/sc

With these steps, the SHOUTcast DNAS software is successfully installed on your utho.

### Configure SHOUTcast

Next, you'll need to customize the configuration to define passwords and specify the SHOUTcast port. Here's how to configure SHOUTcast

Open the SHOUTcast configuration file:

        nano sc/sc_serv_basic.conf

This command will open the configuration file for editing.

        {{< file "/home/shoutcast/sc/sc_serv_basic.conf" conf >}}
        ; NOTE: for any relative paths specified are relative to
        ; sc_serv and not to where the conf file is being stored

        ; here we will setup where the log and other related files
        ; will be stored. make sure that these folders exist else
        ; sc_serv will throw an error and will close itself down.
        ; we will make the logs save to the sc_serv2 directory
        logfile=logs/sc_serv.log
        w3clog=logs/sc_w3c.log
        banfile=control/sc_serv.ban
        ripfile=control/sc_serv.rip


        ; for testing we will make the server only work locally
        ; (i.e. localhost / 127.0.0.1) though if this is left out
        ; or set to publicserver=always then we attempt to make a
        ; connection to the YP for listing - do not forget to add
        ; in a 'streamauthhash' value for any public streams made
        ;publicserver=never


        ; if you're wanting to use a different port to use for any
        ; connections then you can use this option e.g. to use 80
        ; otherwise port 8000 is used as the default to listen on.
        ;portbase=80


        ; password used by sc_trans or the Winamp dsp plug-in
        ; NOTE: remember to change this to something else
        password=testing


        ; password used for accessing the administration pages
        ; NOTE: remember to change this to something else
        adminpassword=changeme


        ; now we will specify the details of the stream we're going
        ; to serve which can be done as follows
        streamid=1
        streampath=/test.aac

        ; or

        ; it can be done like this which is how it needs to be done
        ; if you are going to provide multiple streams from sc_serv
        ;streamid_1=1
        ;streampath_1=/test.aac
        ;streamid_2=2
        ;streampath_2=/test2.aac

        {{< /file >}}

Adjust the `password` and `adminpassword` variables according to your preferences.

Ensure the `portbase` variable is configured to utilize a port not in use for other purposes. The default port for SHOUTcast is 8000.

Note: If you modify the `portbase` variable to a value other than 8000, remember to uncomment it by removing the semicolon preceding the variable.

Save the modifications to the SHOUTcast configuration file by pressing Control-X, followed by Y.

With the configuration set and saved, you can now proceed to start the server.

### Start SHOUTcast

Now, you can initiate the SHOUTcast server. Here's the process:

Launch your SHOUTcast server within a [screen session](/docs/guides/using-gnu-screen-to-manage-persistent-terminal-sessions/).. Enter the following command to enter a screen session:

        screen

Start the SHOUTcast server by executing the following command:

        ./sc_serv sc_serv_simple.conf

After issuing the start command, monitor the startup output, which should conclude with:

        2011-11-02 14:50:03     I       msg:[MICROSERVER] Listening for connection on port 8000
        2011-11-02 14:50:03     I       msg:[MICROSERVER] Listening for connection on port 8001

At this point, you can detach from your screen session. To do so, press and hold down the Control key, followed by pressing A, then release both keys, and press D.

You'll return to the command prompt outside of your screen session. If you need to reattach later on, simply type:
        screen -raAd

Your SHOUTcast server is now up and running! You can connect to it and commence your broadcast.

## SHOUTcast Transcoder

The SHOUTcast Transcoder enables you to schedule DJ play times, broadcast automatic playlists at specific intervals, schedule time slots for relayed broadcasts, and more.

Note: To encode streams in MP3 format, a license key from WinAmp must  [purchase a license key from WinAmp, which costs \$5 USD](http://wiki.winamp.com/wiki/SHOUTcast_DNAS_Transcoder_2#Registering_for_MP3_Stream_Encoding). Which costs $5 USD.

### Download and Install SHOUTcast Transcoder

We'll utilize the same shoutcast user to configure the Transcoder software. Here's how to download and install the transcoder:

Change directories with the following command:

        cd /home/shoutcast

Create a new directory for the transcoder:

        mkdir sct

Download the SHOUTcast transcoder archive:

        wget http://download.nullsoft.com/shoutcast/tools/sc_trans_linux_10_07_2011.tar.gz

Extract the SHOUTcast transcoder files:

        tar -xzf sc_trans_linux_10_07_2011.tar.gz -C sct

Adjust ownership from `root` to the SHOUTcast user:

        chown -R shoutcast.shoutcast /home/shoutcast/sct

Change directories:

        cd sct

Set permissions:

        chmod a+x sc_trans

With these steps, the SHOUTcast transcoder is now successfully installed on your utho.

### Configure the SHOUTcast Transcoder

Here's a step-by-step guide to basic configuration:

Open the configuration file by typing:

        nano /home/shoutcast/sct/sc_trans_basic.conf

You can adjust the bitrate to alter the music's sound quality and limit bandwidth consumption. If you've purchased MP3 licensing, you can modify the encoder section to include MP3 encoding and your unlock data:

         {{< file "/home/shoutcast/sct/sc_trans_basic.conf" conf >}}  
         ; for testing we will only setup a single encoder though it
         ; is easy to add in additional encoder configurations and
         ; we are using an aac plus encoder as the default due to
         ; the licensing requirements for mp3 encoding as detailed
         ; in sc_trans.txt - section 2.5).
         encoder_1=aacp
         encoder_2=mp3
         bitrate_1=56000
         bitrate_2=56000

         unlockkeyname=YourUnlockName
         unlockkeycode=YourUnlockCode

         {{< /file >}}

Next, update the sc_trans to sc_serv connection details:

         {{< file "/home/shoutcast/sct/sc_trans_basic.conf" conf >}}
         ; this is where we define the details required for sc_trans
         ; to connect to the sc_serv instance being used where the
         ; details must match those specified in sc_serv_basic.conf
         outprotocol_1=3
         serverip_1=127.0.0.1
         ; default is 8000, if not change to sc_serv's 'portbase'
         serverport_1=8000
         ; this is the same as 'password' in sc_serv_basic.conf
         password_1=testing
         ; this is the same as 'streamid' in sc_serv_basic.conf for
         ; the stream we are acting as the source for
         streamid_1=1
         ; this is a name for the source we're creating and is used
         ; with the AJAX control api or can be left blank to get a
         ; generic name created in the form of 'endpointX' where 'X'
         ; is the index of the created source from sc_trans lists.
         endpointname_1=/Bob

        {{< /file >}}

Optionally, update your stream information:

         {{< file "/home/shoutcast/sct/sc_trans_basic.conf" conf >}}
         ; here you would provide any information to fill in details
         ; provided to clients about the stream. it us up to you what
         ; is entered though do not do anything which will annoy, etc
         streamtitle=My Test Server
         streamurl=http://www.shoutcast.com
         genre=Misc

         {{< /file >}}

Set your playlist file for automated streaming:

         {{< file "/home/shoutcast/sct/sc_trans_basic.conf" conf >}}
         ; here we specify a playlist to use as the master list from
         ; which to play files from.
         playlistfile=playlists/main.lst
         {{< /file >}}

Configure the port, username, and password for transcoder admin panel access:

          {{< file "/home/shoutcast/sct/sc_trans_basic.conf" conf >}}
          ; these options will allow you access the admin interfaces
          ; of sc_trans though also allows the 'testui' example to be
          ; accessed. remember to change the password, etc as needed
          adminport=7999
          adminuser=admin
          adminpassword=goaway

          {{< /file >}}

Save the changes to the SHOUTcast configuration file by pressing Control-X, then Y.

If you're using an automated playlist, upload your music files to the `/home/shoutcast/transcoder/music` directory.

For an automated playlist, create a playlist file. Here's an example:

          {{< file "/home/shoutcast/sct/playlists/playlist.lst" >}}

This sample playlist serves as the primary playlist utilized by sc_trans to select.
The files for generating its output stream.
Remember to utilize the appropriate path format for your operating system and ensure.
That the desired files are located in the designated location.

## e.g.
         ../music/shoutcast.mp3

 In this scenario, we'll presume that all files linked to the playlist reside in a single folder and share the .mp3 extension. However, you have the option to specify files explicitly or refer to a tool if needed. For further details on playlist functionality, consult section 7.1 of the sc_trans.txt file.

Ensure to modify this accordingly to reflect the files you wish to utilize when experimenting with the sc_trans_playlist.conf example, particularly recommended for full-length files.

### Start SHOUTcast Transcoder

Once your transcoder is configured and prepared, you can initiate it. To run the transcoder as a daemon, execute the following command, replacing `sc_trans_basic.conf` with the name of your configuration file:


    ./sc_trans daemon ./sc_trans_basic.conf

If no errors arise, you should observe output resembling the line below, where XXXX represents the PID:


    sc_trans going daemon with PID [XXXX]

To halt the transcoder, simply execute a kill command:

    kill -15 PID

## SHOUTcast Source DSP
The SHOUTcast Source DSP plugin is designed for compatibility with WinAmp versions 5.5 and above. This plugin empowers you to utilize WinAmp as a source for your sc_serv (DNAS) or sc_trans (Transcoder). Additionally, it facilitates capturing audio input from your sound card, including its line-in or microphone inputs. Prior to utilizing the DSP WinAmp plugin, you must have a functional installation of either the DNAS on its own or the Transcoder feeding into a DNAS installation. The DSP plugin can be downloaded from the bottom section of the [broadcast tools page](http://www.shoutcast.com/broadcast-tools).

Instructions for installation and configuration can be found in the [WinAmp wiki](http://wiki.winamp.com/wiki/Source_DSP_Plug-in#Installing_the_Plug-in).
