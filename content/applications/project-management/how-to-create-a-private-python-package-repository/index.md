---
slug: "Unlock the Python Power: Build Your Own Private Package Paradise"
description: 'In this guide, discover the ins and outs of setting up your very own private Python package repository. Unlock the steps needed to create and manage your repository seamlessly'
keywords: ["pip", "Python", "PyPA", "virtualenv", "package management"]
tags: ["python", "apache"]
modified: 2024-04-11
modified_by:
  name: Pawan Kumar
published: 2024-04-11
title: 'How to Create a Private Python Package Repository'
authors: ["Pawan Kumar"]
---

## How Does Python Handle Package Management?

Python offers various tools for package management, with Pip being the go-to choice for many developers. `Pip` simplifies the process of installing and updating software packages, ensuring precise management of package versions and dependencies.

PyPI (Python Package Index) serves as a vast repository of user-contributed packages, accessible via `pip install package`. This guide walks you through the fundamentals of creating a Python package and demonstrates how to establish a private PyPI repository using PyPiServer on a Utho server.

## Before You Begin
Before starting, follow our [Getting Started](/docs/products/platform/get-started/) guide to set up your Utho's timezone.

Ensure you have Python 3 installed, along with `pip` and `setuptools`. Python 3.4 and later versions come with pip by default. On Debian-based systems, install pip using `sudo apt install python3-pip`.

This guide uses Apache 2.4. Older versions may have different configurations.

## Minimalist Python Package

A Python package typically consists of a` __init__.py` file, containing code for interacting with users.

Begin by creating a directory with the name you want for your package. For instance, in this guide, we'll use **utho_example** as an illustration.

        mkdir utho_example

{{< note respectIndent=false >}}
If you plan to make your package public, there are some things to think about when choosing a name. According to the official documentation, it's best to use lowercase letters, which is unique to PyPI. You can also use underscores to separate words if necessary.
{{< /note >}}

2.  Go into the directory you just created. Inside it, create a file named `setup.py`. Then, make another directory named `utho_example` within it. Inside this directory, add a file named `__init__.py`. Your directory structure should now look like this:

        {{< output >}}
        utho_example/
        utho_example/
        __init__.py
        setup.py
        setup.cfg
        README.md
        {{< /output >}}

3. Next, we'll customize the `setup.py` file to provide essential details about your Python package repository.

       {{< file "utho_example/setup.py" >}}
       from setuptools import setup

       setup(
       name='utho_example',
       packages=['utho_example'],
       description='Hello world enterprise edition',
       version='0.1',
       url='http://github.com/example/utho_example',
       author='Utho',
       author_email='docs@utho.com',
       keywords=['pip','utho','example']
       )
       {{< /file >}}

4.  In `__init__.py`, insert a sample function:

        {{< file "utho_example/utho_example/__init__.py" >}}
        def hello_word():
        print("hello world")

        {{< /file >}}

5.  The `setup.cfg` file informs PyPI that the README is in Markdown format.

        {{< file "setup.cfg" >}}
        [metadata]
        description-file = README.md
        {{< /file >}}

Optionally, you can include a `LICENSE.txt` file or add details to `README.md`. This is good practice for documentation and can be useful if you decide to upload the Python package to the public PyPI repository in the future.

Before your Python package repository can be downloaded from your server, it needs to be compressed. Compress the package:

        python setup.py sdist

A **tar.gz** file will be created in `~/utho_example/dist/`.

## Install PyPI Server

Next, let's prepare a server to host a package index. We'll use `pypiserver`, which simplifies setting up a package index on a server using the Bottle framework.

1.  If you haven't already, install virtualenv:

        pip install virtualenv

2.  Create a new directory to store Python packages and Apache files. Inside this directory, create a new virtual environment named `venv`, then activate it:

        mkdir ~/packages
        cd packages
        virtualenv venv
        source venv/bin/activate

3.  Download the pypiserver package within the newly created virtual environment using `pip`:

        pip install pypiserver

{{< note respectIndent=false >}}
Another option is to [download pypiserver from Github](https://github.com/pypiserver/pypiserver). Once downloaded, navigate into the pypiserver directory and install the packages using `python setup.py install`.
{{< /note >}}

4.  Move the `utho_example-0.1.tar.gz` file into the ~/packages directory.

        mv ~/utho_example/dist/utho_example-0.1.tar.gz ~/packages/

5.  Test the server by running:

        pypi-server -p 8080 ~/packages

6.  Currently the server is listening on all IP addresses. In a web browser, navigate to `192.0.2.0:8080`, where `192.0.2.0` is the public IP of your Utho. The browser should display:

You can now install the `utho_example` package by specifying an external URL `pip install --extra-index-url http://192.0.1.0:8080/simple/ --trusted-host 192.0.2.0 utho_example`.

## Securing Access: Apache Authentication with Passlib

1.  First, install Apache and `passlib` to enable password-based authentication for uploads. Ensure you're still in your activated virtual environment (you should see `(venv)` before the terminal prompt), and then run these commands:

        sudo apt install apache2
        pip install passlib

2.  Next, create a password for authentication using `htpasswd`. Save the generated password as htpasswd.txt in the `~/packages` directory. You'll be prompted to enter the desired password twice.

        htpasswd -sc htpasswd.txt example_user
        New password:
        Re-type new password:

3.  Lastly, install and activate `mod_wsgi` to allow Bottle, a WSGI framework, to communicate with Apache:

        sudo apt install libapache2-mod-wsgi
        sudo a2enmod wsgi

4.  In the `~/packages` directory, make a `pypiserver.wsgi` file to establish a connection between pypiserver and Apache:

        {{< file "packages/pypiserver.wsgi" >}}
        import pypiserver
        PACKAGES = '/absolute/path/to/packages'
        HTPASSWD = '/absolute/path/to/htpasswd.txt'
        application = pypiserver.app(root=PACKAGES, redirect_to_fallback=True, password_file=HTPASSWD)
        {{< /file >}}

5. Then, create a configuration file for the pypiserver in `/etc/apache2/sites-available/`:

        {{< file "/etc/apache2/sites-available/pypiserver.conf" >}}
        <VirtualHost *:80>
        WSGIPassAuthorization On
        WSGIScriptAlias / /absolute/path/to/packages/pypiserver.wsgi
        WSGIDaemonProcess pypiserver python-path=/absolute/path/to/packages:/absolute/path/to/packages/venv/lib/pythonX.X/site-packages
        LogLevel info
        <Directory /absolute/path/to/packages>
        WSGIProcessGroup pypiserver
        WSGIApplicationGroup %{GLOBAL}
        Require ip 203.0.113.0
        </Directory>
        </VirtualHost>

        {{< /file >}}

Note: The `Require ip 203.0.113.0` directive restricts access to Apache based on IP. To allow unrestricted access, replace it with `Require all granted`. For more intricate access control rules, refer to the access control section in the [Apache documentation](https://httpd.apache.org/docs/2.4/howto/access.html).

{{< note respectIndent=false >}}
The path for the `WSGIDaemonProcess` directive might vary depending on the Python version and virtual environment path.
{{< /note >}}

6.  Grant ownership of the `~/packages` directory to the user **www-data**. This will enable uploads from a client using setuptools:

        sudo chown -R www-data:www-data packages/

7.  If necessary, disable the default site and enable pypiserver:

        sudo a2dissite 000-default.conf
        sudo a2ensite pypiserver.conf

8.  Restart Apache:

        sudo service apache2 restart

By default, the repository should be accessible via 192.0.1.0 on port 80, where 192.0.1.0 is the Utho's public IP address.

## Download From a Client

Remember those long flags you had to use with `pip` to download from a specific repository? Well, you can simplify things by creating a configuration file that contains the IP address of your public server.

1.  On your client computer, go to your home directory and create a .pip directory. Inside this directory, create a file named `pip.conf` with the following content:


        {{< file "pip.conf" >}}
        [global]
        extra-index-url = http://192.0.2.0:8080/
        trusted-host = 192.0.2.0
        {{< /file >}}

2.  Now, you can install the utho_example package:

        pip install utho_example

    {{< note respectIndent=false >}}
When you check the terminal output or use `pip list` to view all packages, you'll notice that the underscore in the package name has been replaced with a dash. This is because `setuptools` automatically converts underscores to dashes using the `safe_name` utility. For more details, you can [see this mailing list thread](https://mail.python.org/pipermail/distutils-sig/2010-March/015650.html).
{{< /note >}}

3. You can also try out the new package in a Python shell:

       {{< output >}}
       >>from utho_example import hello_world
       >>hello_world()
       hello world
       {{< /output >}}

## Uploading Packages Remotely with Setuptools
Instead of relying solely on `scp` to transfer tar.gz files to the repository, you have other options like `twine` and `easy_install` at your disposal.

1.  On your client computer, create a new configuration file named `.pypirc` in your home directory. The remote repository will be named Utho:

        {{< file ".pypirc" >}}
        [distutils]
        index-servers =
        pypi
        utho
        [pypi]
        username:
        password:
        [utho]
        repository: http://192.0.2.0
        username: example_user
        password: mypassword

{{< /file >}}

To upload to the official Python Package Index, you need an account, but you can leave the account information fields blank. Replace *example_user* and *mypassword* with the credentials you set up earlier using `htpasswd`.

2.  To upload from the directory of the Python package:

        python setup.py sdist upload -r utho

If the upload is successful, you'll see the message: Server Response (200): OK in the console.
