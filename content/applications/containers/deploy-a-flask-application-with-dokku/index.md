---
slug: "Flask Soars: Deploy Your App with Dokku Magic"
description: "In this walkthrough, discover how to swiftly deploy a Flask application with SSL and NGINX using Dokku."
keywords: ['docker','containers','nginx', 'heroku', 'PaaS', 'git', 'Platform-as-a-service', 'Platform As a Service']
tags: ["container","docker","ssl","nginx"]
published: 2024-04-04
modified: 2024-04-04
modified_by:
  name: Utho
title: "Deploy a Flask Application with Dokku"
authors: ["Pawan Kumar"]
---
Dokku is a self-hosted Platform-as-a-Service (PaaS) that simplifies application deployment using Git. While similar to Heroku, it doesn't offer auto-scaling. Nonetheless, Dokku is potent, automatically running your app in Docker with minimal web server setup.

This guide covers:

- Building a Flask app that displays 'Hello World!' on the index page
- Installing Dokku on a Utho
- Deploying a Flask app with a WSGI server in a Docker container
- Adding an SSL certificate via Dokku using the Let's Encrypt plugin

## Before You Begin

### On Your Local Computer

{{< note >}}
Dokku v0.12.5 works smoothly with Ubuntu 16.04 x64, Ubuntu 14.04 x64, and Debian 8.2 x64. While CentOS 7 x64 is supported experimentally, certain tasks like configuring SSH keys and virtual hosts may require manual setup via the dokku command line interface. For further details, refer to [the official documentation](http://dokku.viewdocs.io/dokku~v0.12.5/getting-started/installation/).
{{< /note >}}

It's assumed that a [public key](/docs/guides/use-public-key-authentication-with-ssh/) is available. Usually, you'll find it at `~/home/username/.ssh/id_rsa.pub`.

If necessary, install Git:

    sudo apt install git

### On Your Utho

The Dokku install script sets up a `dokku` user on the system, installs Docker, and fetches the necessary image.

1. Download the install script from Dokku, then execute the script:

        wget https://raw.githubusercontent.com/dokku/dokku/v0.12.5/bootstrap.sh
        sudo DOKKU_TAG=v0.12.5 bash bootstrap.sh

        {{< output >}}

        Preparing to install v0.11.6 from https://github.com/dokku/dokku.git...
        For dokku to build containers, it is strongly suggested that you have 1024 megabytes or more of free memory
        If necessary, please consult this document to setup swap: http://dokku.viewdocs.io/dokku/advanced-installation/#vms-with-less-than-1gb-of-memory
        --> Ensuring we have the proper dependencies
        --> Initial apt-get update
        --> Installing docker
        --> NOTE: Using Utho? Docker may complain about missing AUFS support.
        You can safely ignore this warning.
        Installation will continue in 10 seconds.
        ...
        {{< /output >}}

2.  Go to the public IP address of your Utho in a web browser, and input the public key:

{{< note type="alert" respectIndent=false >}}

After executing the installation script, promptly add the public key to Dokku to prevent others from adding their own keys. For unattended installations, please consult the [advanced installation instructions](https://github.com/dokku/dokku/blob/master/docs/getting-started/advanced-installation.md).
{{< /note >}}

3.  
To add more SSH keys, send the output over SSH to the `dokku` user. Replace `example.com` with the IP address of your Utho.

        cat ~/.ssh/id_rsa.pub | ssh dokku@example.com ssh-keys:add new-key

## Create a Flask Application

1.  On your local computer, establish a new project directory:

        mkdir flask-example && cd flask-example

2. Generate a new file named `hello_world.py` that serves 'Hello World!' on the index page.

        {{< file "hello_world.py" python >}}
        import os

        from flask import Flask

        app = Flask(__name__)

        @app.route('/')
        def hello():
        return 'Hello World!'

        if __name__ == '__main__':
        # Bind to PORT if defined, otherwise default to 5000.
        port = int(os.environ.get('PORT', 5000))
        app.run(host='127.0.0.1', port=port)
        {{< /file >}}

3.  Create a `requirements.txt` file to monitor versions of any dependencies of the Flask application. Gunicorn is the WSGI server used to enable proper interaction between Flask and NGINX.

        {{< file "requirements.txt" >}}
        Flask==0.12.1
        gunicorn==19.7.1
        {{< /file >}}

4.  For more complex projects with numerous dependencies utilizing a virtual environment, direct the output of `pip freeze` into `requirements.txt`.

        pip freeze > requirements.txt

### Add a gitignore

Optionally, add a `.gitignore` file to have Git omit caching and virtual environment files from version control.

    {{< file ".gitignore" >}}
    __pycache__/
    *.pyc

    venv/
    {{< /file >}}

### Procfile

Optionally, you can add a `.gitignore` file to instruct Git to exclude caching and virtual environment files from version control.

     {{< file "Procfile" >}}  
     web: gunicorn hello_world:app --workers=4
     {{< /file >}}

### Git Remote

1. Initialize a Git repository:

        git init
        git add .
        git commit -m "Deploy Flask with Dokku"

2.  Add a remote named `dokku` with the username dokku, replacing `example.com` with the public IP address of your Utho:

        git remote add dokku dokku@example.com:flask-example

3.  Verify that the remote is added:

        git remote -v

This command will display the list of remotes.

    {{< output >}}
    dokku   dokku@example-ip:flask-example (fetch)
    dokku   dokku@example-ip:flask-example (push)
    {{< /output >}}

    In summary, the project layout looks like:

        flask-example
        ├── .gitignore
        ├── Procfile
        ├── hello_world.py
        └── requirements.txt

## Create Project on a Dokku Host

1. SSH into your Utho and create the application:

        dokku apps:create flask-example

2.  Ensure that VHOST is enabled.

        dokku domains:enable flask-example

## Deploy a Flask Application

1.  On your local computer, deploy the Flask application by pushing the branch to the `dokku` remote. This process will handle NGINX in the background and expose port `80`:

        git push dokku master

Other local branches can also be deployed, but all branches must be pushed to the master branch of the `dokku` remote:

        git push dokku branch-name:master

2.  Use `curl` to test that the app was successfully deployed by accessing the IP address of your Utho:

        curl example.com

        {{< output >}}
        Hello World!
        {{< /output >}}

###  Securing Your Application: SSL Certificate with Dokku and Let's Encrypt

Continuing with the setup, proceed with the following steps from your Utho:

1.  Install the Let's Encrypt plugin for Dokku:

        sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git

2.  Set the `DOKKU_LETSENCRYPT_EMAIL` environment variable to the email associated with Let's Encrypt:

        dokku config:set flask-example DOKKU_LETSENCRYPT_EMAIL=docs@utho.com

3.  Add the application and domain:

        dokku domains:add flask-example example.com

4.  Generate the SSL certificate. NGINX will commence serving the application over HTTPS on port 443:

         dokku letsencrypt flask-example

5.  Set up a cron job for automatic certificate renewal:

        dokku letsencrypt:cron-job --add

    {{< note respectIndent=false >}}

Ensure your `Dokku` version is 0.5 or higher. Verify this by running dokku version.
{{< /note >}}

## Start, Stop, and Restart Applications

* List all running Dokku applications:

        dokku apps

* Restart an application:

        dokku ps:restart flask-example

* Stop an application:

        dokku ps:stop flask-example

* Restore all applications after a reboot:

        dokku ps:restore

### View Application Logs

View application logs through Dokku or the Docker container.

1.  To view logs through Dokku:

        dokku logs flask-example

2.  List all running Docker containers:

        sudo docker ps -a

3.  Locate the container ID, then execute:

        sudo docker logs container_id

## Scale Applications

Dokku does not scale applications automatically, and by default will only run a single `web` process. To increase the number of containers running your application, you can use the `ps:scale` command.

1.  Check how many workers your application currently has:

        dokku ps:scale flask-example

    {{< output >}}
-----> Scaling for flask-example
-----> proctype           qty
-----> --------           ---
-----> web                1
{{< /output >}}

2.  Scale up to 4 `web` processes:

        dokku ps:scale flask-example web=4

3.  Confirm that the new processes are running:

        {{< output >}}
        -----> Scaling for flask-example
        -----> proctype           qty
        -----> --------           ---
        -----> web                4
        {{< /output >}}

Dokku serves as an open-source alternative to Heroku, particularly suited for small applications. Deploying applications is streamlined through Git remote pushes. Under the hood, elements like Docker and NGINX are abstracted to expedite deployment. While this guide covers basic deployment, Dokku offers additional features like [pre-deploy hooks](http://dokku.viewdocs.io/dokku/advanced-usage/deployment-tasks/) and database linking.
