---
slug: "Stay Ahead of the Game: Track Your Website's Evolution with Huginn Agents"
description: "Unlock Efficiency: Learn How to Set Up and Customize Huginn on utho, Your Self-Hosted Solution for Streamlining Online Tasks, Comparable to IFTTT"
keywords: ['huginn website agent']
tags: ['ubuntu', 'docker']
published: 2024-05-14
modified_by:
  name: Pawan Kumar
title: "Monitor Your Website Changes with Huginn Agents"
title_meta: "How to Monitor Your Website Changes with Huginn Agents"
authors: ["Pawan Kumar"]
---
[Huginn](https://github.com/huginn/huginn) offers an open-source, self-hosted solution for automating online tasks, akin to IFTTT and Zapier, with enhanced customization capabilities. By hosting Huginn on your own server, you gain complete control over your automation processes. This comprehensive guide walks you through setting up your Huginn instance and delves into configuring agents for tailored notifications.

## Before You Begin

If you haven't already, create a utho account and Compute Instance.

Refer to our Setting Up and Securing a Compute Instance guide to ensure your system is up to date. You may also want to adjust the timezone, set up your hostname, create a restricted user account, and fortify SSH access.

Huginn is compatible with Debian and Ubuntu Linux distributions, and this guide is tailored to these platforms.

Note: This guide is designed for non-root users. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the sudo command, consult the [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## Install Docker and Docker Compose

This guide utilizes [Docker](https://www.docker.com/) for running Huginn. Huginn offers an [official Docker setup](https://github.com/huginn/huginn/blob/master/doc/docker/install.md) to simplify the process of getting your instance up and running. If Docker is new to you, it's advisable to familiarize yourself with it through the [Introduction to Docker](/docs/guides/introduction-to-docker/) guide.

While it's possible to install Huginn manually, this guide opts for the Docker method. This decision is because Huginn doesn't support manual installation for the latest Debian and Ubuntu releases. However, if you prefer the [manual installation instructions](https://github.com/huginn/huginn/tree/master/doc/manual) , Huginn provides instructions for that as well.

## Try Out Huginn

Once Docker is installed, you can swiftly spin up a Huginn instance to test it out. Running Huginn in this manner doesn't establish persistent data storage, meaning any modifications made won't be retained after halting the instance.

Launch the Huginn Docker container.

        docker run -it -p 3000:3000 huginn/huginn

Access `localhost:3000` via a web browser. For remote access to the Huginn instance, utilize an SSH tunnel.

- On Windows, employ the PuTTY tool to configure your SSH tunnel. Refer to the relevant section of the  [Using SSH on Windows](/docs/guides/connect-to-server-over-ssh-on-windows/#ssh-tunnelingport-forwarding) guide, substituting the provided port number with 3000.

- On OS X or Linux, execute the following command to establish the SSH tunnel. Replace `example-user` with your username on the application server and `192.0.2.1` with the server's IP address.

            ssh -L3000:localhost:3000 example-user@192.0.2.0

Utilize the default credentials to log into your Huginn instance. The credentials are Login: `admin` and `Password`: password.

## Deploy Huginn

Alternatively, Docker facilitates the setup of a comprehensive and persistent Huginn instance. This guide employs Huginn's default database configuration—a MySQL database—providing the simplest route to Huginn utilization.

Create a `.env.huginn` file mirroring the example file's contents. This file specifies the SMTP credentials for Huginn to utilize, enabling email sending. In this example, substitute `example-smpt-domain.com`, `example-smtp-username`, `example-smtp-password`, and `example-smtp-host` with the appropriate credentials for your SMTP.

        RAILS_ENV=production

        SMTP_DOMAIN=example-smtp-domain.com
        SMTP_USER_NAME=example-smtp-username
        SMTP_PASSWORD=example-smtp-password
        SMTP_SERVER=example-smtp-host.com
        SMTP_PORT=587
        SMTP_AUTHENTICATION=plain
        SMTP_ENABLE_STARTTLS_AUTO=true

        EMAIL_FROM_ADDRESS=huginn@example-smtp-domain.com

You have the option to establish your SMTP server by adhering to the [Email with Postfix, Dovecot, and MySQL/MariaDB](/docs/guides/email-with-postfix-dovecot-and-mysql/) guide.

Alternatively, you can opt for a third-party SMTP service such as Mailgun. Below is an example of configuring [Mailgun](https://www.mailgun.com/) SMTP account using the aforementioned setup.

        RAILS_ENV=production

        SMTP_DOMAIN=example-domain.mailgun.org
        SMTP_USER_NAME=postmaster@example-domain.mailgun.org
        SMTP_PASSWORD=example-mailgun-password
        SMTP_SERVER=smtp.mailgun.org
        SMTP_PORT=587
        SMTP_AUTHENTICATION=plain
        SMTP_ENABLE_STARTTLS_AUTO=true

        EMAIL_FROM_ADDRESS=huginn@example-domain.mailgun.org

        {{< content "email-warning-shortguide" >}}

Set up a Docker volume specifically for Huginn's database.

        docker volume huginn-data

Launch the Huginn Docker container while integrating the database volume. Here, the Docker port **3000** is mapped to the server's port **80**, the standard port for HTTP connections.

        docker run -d -p 80:3000 --restart=always --env-file .env.huginn -v huginn-data:/var/lib/mysql huginn/huginn

The `--restart=always` option instructs Docker to automatically restart the application whenever the server restarts. This feature remains functional unless you manually stop the Docker container using the command `docker stop example-container-id`. You can obtain the container ID via the `docker ps` command.

Access the URL associated with your server, which can either be your server's domain name or its IP address.

Log in using the default credentials — `admin` as the username and password as the `password`.

You can then modify the account credentials through the **Account** option located in the upper right corner. Additionally, ensure to provide an email address for your account if you wish to receive notifications or enable account recovery via email.

## Manage Huginn Agents

Huginn includes a variety of example agents to aid in familiarizing yourself with its functionalities. It's recommended to review each of these to grasp how Huginn agents are configured.

Agents come in a few different roles. Broadly, agents are either event sources, event receivers, or a combination of both.

Agents serve different roles, broadly categorized as event sources, event receivers, or a combination of both. For instance, a Weather Agent functions as an event source by fetching weather reports for specific locations and creating events from them. Conversely, a **Trigger Agent** processes incoming events based on certain criteria, while an Event Formatting Agent reshapes incoming events before forwarding them.

Agents can also serve as sources for other agents that dispatch events externally. For example, the Email Agent sends events via email, the **Twilio Agent** dispatches events via SMS, and agents like the Twitter Publish Agent and **Tumblr Publish Agent** post events on social media platforms.

The subsequent sections guide you through deploying your custom agents. They demonstrate setting up a series of agents to fetch news headlines and distribute them each morning.

### Source Agent

Choose the **New Agent** option either from the **Agent** menu dropdown or from the bottom of the agent list page.

Select **Website Agent** as the agent type. Upon selection, additional options will become available.

This agent is capable of scraping a website and generating events based on the extracted information. The following steps configure the agent to identify headlines on the [BBC](https://www.bbc.com) news website and generate a series of events from them every morning.

Once an agent type is chosen, Huginn provides reference information in a pane on the right, aiding in learning about different agent types and deciding on suitable options for your use case.

Specify the agent's name — "BBC Source" in this example. Schedule a time for the agent to run; in this case, "5 am" is selected. Under **Scenarios**, choose the **default-scenario** option.

**Under** Options, toggle to the **Toggle View** mode to manually enter JSON. Then, input the following in the text box.

        {
          "expected_update_period_in_days": "1",
          "url": "https://www.bbc.com/",
          "type": "html",
          "mode": "on_change",
          "extract": {
            "url": {
              "css": ".media__link",
              "value": "@href"
            },
            "title": {
              "css": ".media__link",
              "value": "normalize-space(./node())"
            }
          }
        }

Utilize the **Dry Run** option to test the agent. Upon selection, a prompt will appear. Choose Dry Run to generate the agent's results.

**Save** the configuration, and Huginn will redirect you to the list of your agents, where you'll find your new agent listed.


### Format Agent

Repeat the process by selecting **New Agent** again, this time opting for **Event Formatting Agent** as the agent type. Assign a name for the agent — "News Headline Formatter" in this instance.


This agent type allows reshaping incoming events. Since the events scraped by the **Website Agent** might not be in an ideal format, this agent enables restructuring them as necessary.


For **Source**, select the **Website Agent** created earlier ("BBC Source"), and choose "default-scenario" under **Scenarios**.

Toggle to the **Toggle View** mode under **Options** to edit the JSON, and input the following in the text box.

        {
          "instructions": {
            "message": "<a href='https://www.bbc.com/{{url}}'>{{title}}</a>",
            "subject": "News Headline, {{created_at | date:'%m/%d'}}: {{title}}"
          },
          "matchers": [],
          "mode": "clean"
        }

Here and in the subsequent JSON examples, you'll notice that Huginn offers templating capabilities, allowing you to manipulate variable data and utilize functions. Huginn leverages the Liquid template engine for this purpose and provides comprehensive [documentation](https://github.com/huginn/huginn/wiki/Formatting-Events-using-Liquidon formatting and default variables.

Once again, utilize the **Dry Run** option to test the agent configuration.

Click **Save** to finalize the agent setup and return to the agent list.

### Digest Agent

Select **New Agent** once more, and opt for **Digest Agent** as its type. Assign a name for the agent — in this case, "Morning News Digest" is chosen.

A **Digest Agent** consolidates a series of events into a single event. This functionality proves useful as it enables Huginn to dispatch a single email containing all headlines rather than separate emails for each.

Choose a suitable time for the agent to run; "6 am" is a fitting choice for this scenario. Under Sources, pick the **Event Formatting Agent** created earlier ("News Headline Formatter"). Select "default-scenario" under **Scenarios**.

Populate the **Message** text box with the following content:

        Here are your news headlines for {{ 'now' | date: '%m/%d' }}:
        {{ events | map: 'message' | join: ',' }}

Click **Save** to complete the agent setup.

### Delivery Agent

Once more, select **New Agent**. This time, opt for **Email Agent** as the type, and assign a name for the agent — here, "News Email Agent" is chosen.

This agent functions precisely as expected — delivering events as emails. In this instance, it receives an event from the **Digest Agent** and promptly sends it.

Under **Sources**, choose the **Digest Agent** created earlier ("Morning News Digest"), and select "default-scenario" for **Scenarios**.

Populate the **Options** text box with the following content:

        {
          "subject": "Your News Headlines for {{ 'now' | date: '%m/%d' }}",
          "expected_receive_period_in_days": "2"
        }

Click **Save** to finalize the agent setup.

### Complete the Agent Setup

Ensure that the relevant users have valid email addresses entered.

On the page listing your agents, utilize the **Actions** menu for the **Website Agent** ("BBC Source") to select **Run**. Repeat the same for the **Digest Agent** ("Morning News Digest") and **Email Agent** ("News Email Agent").

Enjoy the convenience of receiving news headlines in your email inbox every morning.

## Conclusion

You now have active Huginn agents. Huginn boasts a plethora of notification possibilities, ranging from diverse sources to delivery options like email, text (via Twilio), Twitter, and Tumblr.

For further exploration on maximizing Huginn's capabilities, two invaluable resources stand out. Firstly, delve into Huginn itself, which offers extensive information embedded within its various agent types. Secondly, the [Huginn wiki](https://github.com/huginn/huginn/wiki) provides access to numerous helpful articles and examples, inspiring ideas for leveraging your Huginn instance.
