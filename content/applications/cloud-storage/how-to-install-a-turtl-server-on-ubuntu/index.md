---
slug: "turtl-server-installation-ubuntu"
description: 'Learn how to install Turtl, a privacy-focused cloud storage service, on an Ubuntu distribution with this comprehensive guide.'
keywords: ["install turtl", " cloud-based storage", " monitor system security", " ubuntu"]
tags: ["ubuntu"]
modified: 2024-03-19
modified_by:
  name: Utho
published: 2024-03-19
title: 'Installing Turtl Server on Ubuntu: A Step-by-Step Guide'
authors: ["Pawan Kumar"]
---

[Turtl](https://turtlapp.com/docs) provides an open-source solution to traditional cloud storage services. Prioritizing privacy, Turtl offers a secure space to store and manage your passwords, bookmarks, and pictures. By hosting your own Turtl server on a secure Utho, you gain control over your security.

The Turtl server is constructed using Common Lisp and employs low-level encryption based on the Stanford JavaScript Crypto Library. If encryption is a top concern for you, it's essential to review the details provided in the [encryption specifics](https://turtlapp.com/docs/security/encryption-specifics/) section of the official documentation.

## Before You Begin

1.  If you have not already done so, create a Utho account and Compute Instance.

1.  Refer to our guide for updating your system. Additionally, consider setting the timezone, configuring your hostname, creating a restricted user account, and enhancing SSH security access.

### Install Dependencies:

To set up the Turtl server, you'll need to build it from the source. Begin by downloading all necessary dependencies along with git:

    apt install wget curl libtool subversion make automake git


### Libuv, RethinkDB, Clozure Common Lisp, QuickLisp:


#### Libuv

Fetch the Libuv package from the official repository:

    wget https://dist.libuv.org/dist/v1.13.0/libuv-v1.13.0.tar.gz
    tar -xvf libuv-v1.13.0.tar.gz

Compile the package from its source code:

    cd libuv-v1.13.0
    sudo sh autogen.sh
    sudo ./configure
    sudo make
    sudo make install

Once the package is compiled, execute `sudo ldconfig` to update the shared library cache.

#### RethinkDB
[RethinkDB](https://rethinkdb.com/faq/) is a versatile JSON database. As per the Turtl [documentation](https://turtlapp.com/docs/server/), installing RethinkDB is all that's required; Turtl handles the rest of the setup.

RethinkDB offers community-maintained packages across most distributions. On Ubuntu, you'll need to add RethinkDB to your list of repositories:

    source /etc/lsb-release && echo "deb http://download.rethinkdb.com/apt $xenial main" | sudo tee /etc/apt/sources.list.d/rethinkdb.list
    wget -qO- https://download.rethinkdb.com/apt/pubkey.gpg | sudo apt-key add -

Go to your sources.list directory and include your Ubuntu version:

    vi /etc/apt/sources.list.d/rethinkdb.list
    deb http://download.rethinkdb.com/apt xenial main

Update apt and install RethinkDB:

     sudo apt update
     sudo apt install rethinkdb

Navigate to /etc/rethinkdb/ and rename default.conf.sample to default.conf.

    sudo mv /etc/rethinkdb/default.conf.sample /etc/rethinkdb/default.conf

Restart the rethinkdb.service daemon:

    sudo systemctl restart rethinkdb.service

#### Clozure Common Lisp

For this installation you will need to install Clozure Common Lisp (CCL):

 svn co http://svn.clozure.com/publicsvn/openmcl/release/1.11/linuxx86/ccl

As per the CCL [documentation](https://ccl.clozure.com/download.html), you can substitute `linuxx86` with another platform such as solarisx86.

Quickly verify if CCL has been installed correctly by updating the sources:

    cd ccl
    svn update

Move ccl to /usr/bin to enable running ccl from the command line:

    cd ..
    sudo cp -r ccl/ /usr/local/src
    sudo cp /usr/local/src/ccl/scripts/ccl64 /usr/local/bin

Now, executing ccl64 or ccl, depending on your system, will initiate a Lisp environment:

    Utho@localhost:~$ ccl64
    Welcome to Clozure Common Lisp Version 1.11-r16635  (LinuxX8664)!

CCL is created and managed by Clozure Associates. For more details about CCL, visit http://ccl.clozure.com. To inquire about Clozure's Common Lisp consulting services, email info@clozure.com or visit http://www.clozure.com.

To exit the environment, simply type (quit).

#### QuickLisp and ASDF

Create a user named `turtl`:

    adduser turtl
    su turtl

QuickLisp serves a similar purpose in Lisp as **pip** does in Python. Turtl fetches its server dependencies via Quicklisp. Additionally, ASDF is utilized for building Lisp software.

    wget https://beta.quicklisp.org/quicklisp.lisp

    ccl64 --load quicklisp.lisp

Upon successfully executing the above steps, the CCL environment will open, displaying the following output:

{{< output >}}

      ==== quicklisp quickstart 2015-01-28 loaded ====

      To continue with installation, evaluate: (quicklisp-quickstart:install)

      For installation options, evaluate: (quicklisp-quickstart:help)

      Welcome to Clozure Common Lisp Version 1.11-r16635  (LinuxX8664)!

CCL is developed and maintained by Clozure Associates. For further information about CCL, visit http://ccl.clozure.com. To inquire about Clozure's Common Lisp consulting services, email info@clozure.com or visit http://www.clozure.com.

{{< /output >}}

Once you're in the CCL environment, install QuickLisp using the following command:

    (quicklisp-quickstart:install)

After the installation completes, add Quicklisp to your init file.

    (ql:add-to-init-file)

Once you've confirmed the settings, Quicklisp will start automatically when `ccl64` starts. You can now `(quit)` out of CCL for the time being.

Download ASDF:

    wget https://common-lisp.net/project/asdf/asdf.lisp

Load and install asdf.lisp in your CCL environment by following these steps:

    ccl64 --load quicklisp.lisp
    (load (compile-file "asdf.lisp"))
    (quit)

### Install Turtl

Clone Turtl from the GitHub repository by executing the following command:

    git clone https://github.com/turtl/api.git

Create a file named launch.lisp within the /api directory and paste the following commands:

    touch launch.lisp
    vi launch.lisp

    (pushnew "./" asdf:*central-registry* :test #'equal)
    (load "start")

Turtl doesn't include all dependencies by default. Instead, the Turtl community offers a list of dependencies. Clone these into `/home/turtl/quicklisp/local-projects`:

    echo "https://github.com/orthecreedence/cl-hash-util https://github.com/orthecreedence/cl-async https://github.com/orthecreedence/cffi https://github.com/orthecreedence/wookie https://github.com/orthecreedence/cl-rethinkdb https://github.com/orthecreedence/cl-libuv https://github.com/orthecreedence/drakma-async https://github.com/Inaimathi/cl-cwd.git" > dependencies.txt

for repo in `cat dependencies.txt`; do `git clone $repo`; done

Edit the `/home/turtl/.ccl-init.lisp` to include:

    (cwd "/home/turtl/api")
    (load "/home/turtl/api/launch")

The first line instructs Lisp to utilize the `cl-cwd` package, which was cloned, to switch the current working directory to `/home/turtl/api`. You're free to modify this path, but maintain consistent naming conventions. The second line loads your `launch.lisp`, enabling the loading of `asdf` for Turtl to function.

Generate the default Turtl configuration file by executing the following command:

    cp /home/turtl/api/config/config.default.lisp /home/turtl/api/config/config.lisp

The `config.lisp` file stores server configurations. To connect to your Utho from a Turtl desktop or mobile client, you must add the Utho's public IP address to:

      (defvar *site-url* "http://1.0.0.0:8181"
       "The main URL the site will load from.")

Navigate to your home directory and run `ccl64`. This will automatically initiate the Turtl server.

On your local device, download the Turtl client app from the [turtl website](https://turtlapp.com/download/) for supported devices and operating systems.

When prompted, input your public-facing IP address (or `http://localhost:8181` if running locally) and create a username.

Congratulations! You now have a fully functional Turtl server. Utilize it to add files, store passwords, and save bookmarks in your private Turtl instance.

### Next Steps

Turtl lacks extensive official documentation. However, Fred C has crafted an installation guide for Turtl on Debian, which is also beneficial for configuring it on Ubuntu 16.10. Check out the Turtl Google Group and Fred C's guide for more insights:

*  [Fred C's Install on Debian Guide](https://groups.google.com/forum/#!topic/turtl/q3kAYnAcH0s)
*  [Turtl Google Group](https://groups.google.com/forum/#!forum/turtl)
