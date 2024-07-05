---
slug: "Beaconing Brilliance: Monitoring Your Salt Minions"
description: 'This guide demonstrates monitoring Salt minions using beacons. Learn to configure alerts for various system resources, enabling notifications through messaging services like Slack.'
keywords: ['salt','saltstack','minion','minions','beacon','beacons','reactor','reactors','monitor','configuration drift','slack']
tags: ["monitoring","automation","salt"]
published: 2024-04-02
modified: 2024-04-02
modified_by:
  name: Utho
title: "Beaconing Brilliance: Keeping an Eye on Your Salt Minions"
authors: ["Utho"]
---

Every action carried out by Salt, such as applying a highstate or restarting a minion, triggers an event. *Beacons*, on the other hand, emit events for non-Salt processes like system state changes or file modifications. This guide harnesses Salt beacons to alert the Salt master of minion changes and utilizes Salt reactors to respond to those changes.

## Before You Begin
If you haven't set up a Salt master and minion yet, follow the initial steps outlined in our [Getting Started with Salt - Basic Installation and Setup](/docs/guides/getting-started-with-salt-basic-installation-and-setup/)  guide.

{{< note respectIndent=false >}}
The procedures outlined in this guide necessitate root privileges. Ensure you execute the following steps as `root` or with the `sudo` prefix. For more details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Example 1: Preventing Configuration Drift

Configuration drift arises when there are undocumented changes to a system configuration file. Salt can mitigate configuration drift by promptly reverting a file to a safe state upon modification. To achieve this, we first need to delegate file management to Salt. This section employs an NGINX configuration file as an illustration, but you can select any file for this purpose.

### Manage Your File

1.  Create a directory for your managed files in `/srv/salt/files` on your Salt master.

        mkdir /srv/salt/files

1.  Place your `nginx.conf` file, or any other file you wish to manage, in the `/srv/salt/files` directory on your Salt master.

1.  Create a state file on your Salt master to manage the NGINX configuration file.

        {{< file "/srv/salt/nginx_conf.sls" yaml >}}
        /etc/nginx/nginx.conf:
        file.managed:
        - source:
        - salt://files/nginx.conf
        - makedirs: True
        {{< /file >}}

In this `.sls` file, there are two file paths. The first path is the location of your managed file on your minion. The second path, prefixed with `salt://` under the `source` section, indicates the file path on your master. `salt://` is a shortcut that corresponds to `/srv/salt`.

1.  If not already existing, create a top file on your Salt master and include your `nginx_conf.sls` state.

        {{< file "/srv/salt/top.sls" yaml >}}
        base:
        '*':
        - nginx_conf
        {{< /file >}}

1. Execute a highstate from your Salt master to execute the `nginx_conf.sls` state on your minions.

        salt '*' state.apply

### Create a Beacon

1.  To receive notifications about file changes, you'll require the Python `pyinotify` package. Develop a Salt state responsible for installing the `pyinotify` package on your minions.

        {{< file "/srv/salt/packages.sls" >}}
        python-pip:
        pkg.installed

        pyinotify:
        pip.installed:
        - require:
        - pkg: python-pip
        {{</ file >}}

    {{< note respectIndent=false >}}
The inotify beacon is compatible only with operating systems that support the inotify kernel feature. Currently, this excludes FreeBSD, macOS, and Windows.
{{< /note >}}

1.  Begin by creating a `minion.d` directory on the Salt master to house the beacon configuration file.

        mkdir /srv/salt/files/minion.d

1.  Next, develop a beacon that will generate an event each time the `nginx.conf` file undergoes changes on your minion. Create the `/etc/salt/minion.d/beacons.conf` file and insert the following lines:

        {{< file "/etc/salt/minion.d/beacons.conf" yaml >}}
        beacons:
        inotify:
        - files:
        /etc/nginx/nginx.conf:
        mask:
        - modify
        - disable_during_state_run: True
        {{< /file >}}

1.  To enforce this beacon on your minions, establish a new `file.managed` Salt state:

        {{< file "/srv/salt/beacons.sls" >}}
        /etc/salt/minion.d/beacons.conf:
        file.managed:
        - source:
        - salt://files/minion.d/beacons.conf
        - makedirs: True
        {{</ file >}}

1.  Add the new `packages` and `beacons` states to your Salt master's top file.:

        {{< file "/srv/salt/top.sls" yaml >}}
        base:
        '*':
        - nginx_conf
        - packages
        - beacons  
        {{< /file >}}

1. Execute a highstate from your Salt master to apply these changes across your minions.

        salt '*' state.apply

1. Open another shell to your Salt master and commence the Salt event runner. This will be used to monitor file change events from your beacon.

        salt-run state.event pretty=True

1. On your Salt minion, make a modification to your `nginx.conf` file, then switch to your Salt event runner shell. You should observe an event similar to the following:

        {{< output >}}
        salt/beacon/salt-minion/inotify//etc/nginx/nginx.conf	{
        "_stamp": "2018-10-10T13:53:47.163499",
        "change": "IN_MODIFY",
        "id": "salt-minion",
        "path": "/etc/nginx/nginx.conf"
        }
        {{< /output >}}

Note: The first line represents the event name, including your Salt minion's name and the path to your managed file. We'll utilize this event name in the subsequent section.

1. To revert the `nginx.conf` file to its initial state, apply a highstate from your Salt master.

        salt '*' state.apply nginx_conf

Access your managed file on your Salt minion and confirm that the change has been reverted. We'll automate this final step in the subsequent section.

### Create a Reactor

1. On your Salt master, establish the `/srv/reactor` directory.

        mkdir /srv/reactor

2. Within the `/srv/reactor` directory, craft a reactor state file and incorporate the following content:

       {{< file "/srv/reactor/nginx_conf_reactor.sls" yaml >}}
       /etc/nginx/nginx.conf:
       local.state.apply:
       - tgt: {{ data['id'] }}
       - arg:
       - nginx_conf
       {{< /file >}}

In the first line, the file path denotes the name of the reactor, which you can customize as desired. The `tgt` (target) designates the Salt minion that will receive the highstate. Here, the information passed to the reactor from the beacon event is utilized to dynamically select the appropriate Salt minion ID. This information is available within the `data` dictionary. The `arg` (argument) represents the name of the Salt state file created to manage the `nginx.conf` file.

1. Create a `reactor.conf` file on your Salt master and include the new reactor state file.

        {{< file "/etc/salt/master.d/reactor.conf" yaml >}}
         reactor:
         - 'salt/beacon/*/inotify//etc/nginx/nginx.conf':
         - /srv/reactor/nginx_conf_reactor.sls
         {{< /file >}}

This `reactor.conf` file acts as a list of event names matched to reactor state files. In this instance, we've used a glob (*) in the event name, which means any change to an `nginx.conf` file on any minion will trigger the reactor. However, you might prefer to specify a specific minion ID according to your requirements.

4. Restart the salt-master service to apply the `reactor.conf` file.

        systemctl restart salt-master

5.  On your Salt minion, make a change to the `nginx.conf` file. Then, observe your event runner shell to confirm the occurrence of events. Finally, check your nginx.conf file. The changes you made should have automatically reverted.

Congratulations! You now possess the knowledge to manage configuration drift with Salt. All subsequent updates to `nginx.conf` should be performed on the Salt master and applied using `state.apply`.

## Example 2: Monitoring Minion Memory Usage with Slack

Salt includes various system monitoring beacons. Here, we'll monitor a minion's memory usage and send a Slack notification if it exceeds a set threshold. To do this, you'll need to create a Slack bot, get an OAuth token, and set up the bot to send messages on your behalf.

### Configure Your Slack App

1. Go to [Create a Slack app](https://api.slack.com/apps?new_app=1).

1. From the Slack app settings, go to OAuth & Permissions and copy the OAuth Access Token.

1. Take note of the OAuth Access Token.

1.  Under Scopes, select **Send Messages As &lt; your app name &gt;**.

### Create a Beacon

1.  On your Salt master, locate or create the `/srv/salt/files/minion.d/beacons.conf` file and include the following lines:

        {{< file "/srv/salt/files/minion.d/beacons.conf" yaml >}}
        beacons:
        memusage:
        beacon.present:
        - percent: 15%
        - interval: 15
        {{< /file >}}

These settings trigger the beacon event when the memory usage exceeds 80%, and it checks every 15 seconds. Adjust these values as needed for your environment.

1. To implement the beacon on your minions, execute a highstate from your Salt master:

        salt '*' state.apply

1.  If not already done, open another shell into your Salt master and start the event runner:

        salt-run state.event pretty=True

1.  After a few moments, assuming the memory threshold is exceeded, you should observe an event similar to the following:

        {{< output >}}
        salt/beacon/salt-minion/memusage/	{
        "_stamp": "2018-10-10T15:48:53.165368",
        "id": "salt-minion",
        "memusage": 20.7
        }
        {{< /output >}}

Keep in mind that the event's first line contains the minion's name, which we'll reference in the upcoming section.

### Create a Reactor

1.  If you haven't already, create the `/srv/reactor` directory on your Salt master:

        mkdir /srv/reactor

1. Then, create a reactor state file inside the directory and add the following lines. Make sure to customize the `channel`, `api_key`, and `from_name` values according to your preferences. The `api_key` corresponds to the OAuth token obtained in step 3 of the [Configure Your Slack App](#configure-your-slack-app) section:

       {{< file "/srv/reactor/memusage.sls" yaml >}}
       Send memusage to Slack:
       local.slack.post_message:
       - tgt: {{ data['id'] }}
       - kwarg:
       channel: "#general"
       api_key: "xoxp-451607817121-453578458246..."
       message: "{{ data['id'] }} has hit a memory usage threshold: {{ data['memusage'] }}%."
       from_name: "Memusage Bot"
       {{< /file >}}

This configuration utilizes the `data` dictionary provided by the `memusage` event to populate the minion ID and memory usage in the Slack message.

1.  Open or create the `reactor.conf` file. If you already have a `reactor.conf` file from the previous example, leave out the `reactor:` line, but ensure that rest of the configuration is indented two spaces:

        {{< file "/etc/salt/master.d/reactor.conf" yaml >}}
        reactor:
        - 'salt/beacon/*/memusage/':
        - '/srv/reactor/memusage.sls'
        {{< /file >}}

In this example, we've used a wildcard (*) in the event name instead of specifying a specific minion ID. This wildcard means that any memusage event from any minion will trigger the reactor. However, you might prefer to specify a particular minion ID if that fits your requirements better.

1.  After updating the `reactor.conf` file, restart the `salt-master` service to apply the changes.

        systemctl restart salt-master

1.  In your event-runner shell, after a few seconds, you should see an event similar to this:

         {{< output >}}
         salt/job/20181010161053393111/ret/salt-minion	{
         "_stamp": "2018-10-10T16:10:53.571956",
         "cmd": "_return",
         "fun": "slack.post_message",
         "fun_args": [
         {
           "api_key": "xoxp-451607817121-453578458246-452348335312-2328ce145e5c0c724c3a8bc2afafee17",
            "channel": "#general",
            "from_name": "Memusage Bot",
            "message": "salt-minion has hit a memory usage threshold: 20.7."
        }
        ],
        "id": "salt-minion",
        "jid": "20181010161053393111",
        "retcode": 0,
        "return": true,
        "success": true
        }
        {{< /output >}}

1. Open Slack and you should see your app has notified the room.

Congratulations! You've successfully set up monitoring for your Salt minion's memory usage with Slack integration. Remember, Salt can monitor CPU load, disk usage, and much more. Check out the More Information section below for additional resources.
